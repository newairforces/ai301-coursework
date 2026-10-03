# Plan: fix #72 — `verify_password` raises instead of failing closed

Issue: codepath/pathreview-ai301-fa26-s1#72
Reproduction this plan builds on: my repro comment on that issue,
https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5850717106

## Diagnosis

`verify_password` in `core/security.py` hands the stored hash straight to
passlib with no exception handling:

```python
def verify_password(plain_password: str, hashed_password: str) -> bool:
    return bool(pwd_context.verify(plain_password, hashed_password))
```

When the stored value is not a hash passlib can identify, `verify()`
raises instead of returning, and the exception escapes to the caller.
My reproduction pinned this directly — quoting the artifact from my
posted repro comment:

```
>           raise exc.UnknownHashError("hash could not be identified")
E           passlib.exc.UnknownHashError: hash could not be identified

.venv/lib/python3.14/site-packages/passlib/context.py:1132: UnknownHashError
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
```

The control run in the same report rules out a broader breakage: with a
well-formed hash, `verify` returned `True` for the correct password and
`False` for the wrong one. So verification itself is fine; the defect is
confined to the unrecognizable-hash path.

One thing my unit 2 report did **not** establish, which I have since
checked and which changes the scope below: `UnknownHashError` is not the
only exception this call raises on a malformed stored hash. Probing the
shapes directly:

```
$ .venv/bin/python -c "..."
unrecognizable       -> raised passlib.exc.UnknownHashError  (ValueError=True)
empty                -> raised passlib.exc.UnknownHashError  (ValueError=True)
truncated bcrypt     -> raised builtins.ValueError           (ValueError=True)
corrupt body         -> raised builtins.ValueError           (ValueError=True)
None                 -> returned False
```

A value passlib cannot identify at all raises `UnknownHashError`; a value
it identifies as bcrypt but cannot parse raises a plain `ValueError` from
the handler. Both are "a malformed stored hash" in the issue's sense, and
`UnknownHashError` subclasses `ValueError`, so one `except ValueError`
covers both. Catching only `UnknownHashError` would fix the issue's literal
example and leave the truncated-hash case still raising.

## Scope

**In scope:** making `verify_password` fail closed — return `False` — when
the stored hash cannot be verified because it is malformed, and removing
the `xfail(strict=True)` marker from the covering test now that the
behavior is correct.

**Not in scope, deliberately:**

- Changing `hash_password`, the `CryptContext` configuration, or the
  bcrypt scheme. The control run shows these work.
- Auditing or changing the callers of `verify_password`, or how a
  malformed hash could come to be stored in the first place. That is a
  real open question (see Risks) but it is a different issue.
- The `(trapped) error reading bcrypt version` warning passlib 1.7.4
  prints against bcrypt 4.x. It is noise from a packaging mismatch,
  unrelated to this bug, and fixing it means moving a pinned dependency.
- Broadening this to a general "catch everything" guard. A `TypeError`
  from passing a non-string, for instance, is a caller bug and should
  still surface.

## Files

- `core/security.py` — `verify_password`
- `tests/unit/test_security.py` — remove the `xfail` marker on
  `TestSecurity::test_verify_with_wrong_hash_format`

## Approach

1. Wrap the `pwd_context.verify(...)` call in `verify_password` in
   `try` / `except ValueError`, returning `False` from the handler.
   `ValueError` is the narrowest catch that covers both shapes found
   above, since `UnknownHashError` subclasses it.
2. Leave the success path exactly as it is, so a well-formed hash still
   returns passlib's own result.
3. Remove the `@pytest.mark.xfail(strict=True, reason="issue #72 ...")`
   decorator from `test_verify_with_wrong_hash_format`. The marker is
   `strict=True`, so it fails the suite if left on once the test passes —
   removing it is required, not optional.
4. Add one test for the truncated-hash shape, since that path is the one
   my probe found and the existing test does not cover it.

## Relationship to the existing PR

PR #75 (`kragent66-glitch`) is open against this issue and takes the same
route — `except ValueError`, marker removed. I read it before writing
this. I am not trying to race it: the Path Review house rules say credit
attaches to the work I post, and my plan is my own, built from my own
reproduction. Where I differ is the added truncated-hash test, which my
probe above argues for. If #75 lands first I would rather contribute that
test on top of it than duplicate the change.

## Test plan

Re-run the exact steps from my unit 2 repro comment against the change.

1. **Before (already recorded in the repro comment):**
   `pytest ...::test_verify_with_wrong_hash_format -q` reports
   `1 xfailed`, and the same command with `--runxfail` reports
   `1 failed` with
   `passlib.exc.UnknownHashError: hash could not be identified`.
2. **After:** the same command, with the marker removed, must report
   `1 passed` — no `xfail`, no `runxfail` needed. That is the observable
   that flips.
3. **Control, must not change:** the direct `CryptContext` check from the
   repro comment still prints `correct password -> True` and
   `wrong password -> False`. If either moves, the fix is too broad.
4. **New case:** a truncated bcrypt hash returns `False` rather than
   raising.
5. The rest of `tests/unit/test_security.py` passes.

## Risks and unknowns

- **Reachability is still unverified.** I have not established whether any
  live code path can reach `verify_password` with a malformed stored hash,
  so I do not know whether this is a defensive hardening or a reachable
  bug. I said so in my repro comment and it is still true. It does not
  change the fix, but it is why I am not claiming a security impact.
- **`except ValueError` is broader than `except UnknownHashError`.** It
  would also swallow a `ValueError` raised for some other reason inside
  `verify`. I judge that acceptable because every `ValueError` out of this
  call means "this stored hash could not be verified", which is exactly
  the case that should return `False` — but it is a judgement, and I will
  flag it for review in the PR rather than bury it.
- **Python version.** I am on 3.14.7 and `pyproject.toml` declares
  `requires-python >=3.11`. The exception behavior comes from passlib, not
  from the interpreter, so I do not expect a difference, but I have not
  tested on 3.11.

## Deviations

Nothing in the approach changed. All four steps landed as written: the
`try` / `except ValueError` wrap in `verify_password`, the success path
untouched, the `xfail(strict=True)` marker removed, and the truncated-hash
test added. The test plan's observables came out as predicted — the
previously-xfailing test reports `1 passed` with no `--runxfail` needed,
and the control still prints `correct password -> True` /
`wrong password -> False`, so the catch is not too broad.

Two things worth recording that are not approach changes:

- **I expanded the docstring**, which the plan did not mention. The
  function now says it fails closed and why, because the `except ValueError`
  is a deliberate judgement rather than an obvious one, and the next reader
  should not have to reconstruct it from the commit message. This adds no
  behavior.
- **The full `tests/unit/test_security.py` suite passes at 26 tests**,
  which the plan listed as step 5 of the test plan. No existing test needed
  changing, which is the result I wanted: if the catch had been too broad,
  something else in that file would have moved.

Because the build matched the plan, the posted plan comment is still true
as written, so there is nothing to correct on the issue thread.
