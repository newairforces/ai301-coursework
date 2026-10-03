# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of my plan, the branch I built it on, and the evaluation runs that
produced `eval-run.txt`.

---

## Posted upstream

**GitHub username**

newairforces

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5963919174

````
Following up on my reproduction above with a plan.

**Diagnosis.** `verify_password` passes the stored hash straight to
`pwd_context.verify()` with no exception handling, so when passlib cannot
verify the stored value it raises instead of returning. My repro pinned
`passlib.exc.UnknownHashError: hash could not be identified`, and the
control in that report — a well-formed hash still returning `True`/`False`
correctly — rules out anything broader than the malformed-hash path.

One thing I got wrong in my repro and have since checked: `UnknownHashError`
isn't the only exception here. Probing the shapes directly:

```
unrecognizable       -> passlib.exc.UnknownHashError  (ValueError=True)
empty                -> passlib.exc.UnknownHashError  (ValueError=True)
truncated bcrypt     -> builtins.ValueError           (ValueError=True)
corrupt body         -> builtins.ValueError           (ValueError=True)
None                 -> returned False
```

A value passlib can't identify raises `UnknownHashError`; one it identifies
as bcrypt but can't parse raises a plain `ValueError`. Both are malformed
stored hashes, and `UnknownHashError` subclasses `ValueError`, so a single
`except ValueError` covers both. Catching only `UnknownHashError` would fix
the issue's literal example and leave the truncated case still raising.

**Plan.** Wrap the `verify()` call in `core/security.py` in
`try` / `except ValueError` returning `False`, remove the `xfail(strict=True)`
marker from `test_verify_with_wrong_hash_format`, and add one test for the
truncated-hash shape that my probe turned up.

**What I'm deliberately not doing:** touching `hash_password`, the
`CryptContext` config, or the bcrypt scheme — the control shows those are
fine. Not auditing how a malformed hash could come to be stored; that's a
real question but a different issue. Not broadening to a bare `except`,
since a `TypeError` from a non-string argument is a caller bug and should
still surface.

**Test plan.** Re-run the exact commands from my repro comment: the test
that currently reports `1 xfailed` (and `1 failed` under `--runxfail`) must
report `1 passed` with the marker gone. The direct `CryptContext` control
must still print `correct password -> True` / `wrong password -> False` — if
that moves, the catch is too broad.

**Two things flagged rather than buried.** First, `except ValueError` is
broader than `except UnknownHashError` — it would also swallow a `ValueError`
raised inside `verify()` for some other reason. I think that's right, because
every `ValueError` out of this call means "this stored hash could not be
verified", which is the case that should return `False`. But it's a judgement
and I'd rather have it reviewed than assumed. Second, I still haven't
established whether any live code path can reach `verify_password` with a
malformed stored hash, so I don't know if this is defensive hardening or a
reachable bug. It doesn't change the fix, but it's why I'm not claiming a
security impact.

I read PR #75, which takes the same `except ValueError` route. I'm not
trying to race it — my plan is built from my own reproduction, and the one
place I'd add something is the truncated-hash test. If #75 lands first I'd
rather put that test on top of it than duplicate the change.
````

---

## Your branch

**Branch**

fix/72-verify-password-fail-closed

**Evidence**

My Unit 2 reproduction steps, re-run against the built change.

**Before** (from my Unit 2 repro comment, on `main` at commit `f89c06f`).
The covering test was marked `xfail(strict=True)`, so a normal run hid the
exception:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q
1 xfailed, 1 warning in 0.23s
```

Forcing it to execute with `--runxfail` showed the real failure:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q --runxfail
>           raise exc.UnknownHashError("hash could not be identified")
E           passlib.exc.UnknownHashError: hash could not be identified

.venv/lib/python3.14/site-packages/passlib/context.py:1132: UnknownHashError
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
1 failed, 1 warning in 0.18s
```

**After** (on `fix/72-verify-password-fail-closed` at commit `f6f2a01`).
The same test, marker removed, now passes with no `--runxfail` needed —
that is the observable the plan said would flip:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q
1 passed, 1 warning in 0.23s
```

The new truncated-hash case, which the plan added after probing found that
shape raises a plain `ValueError` rather than `UnknownHashError`:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_truncated_hash -q
1 passed, 1 warning in 0.47s
```

**Control, which the plan said must not move.** It did not:

```
$ .venv/bin/python -c "
from core.security import verify_password, hash_password
h = hash_password('password')
print('correct password ->', verify_password('password', h))
print('wrong password   ->', verify_password('nope', h))
print('malformed hash   ->', verify_password('password', 'not_a_valid_bcrypt_hash'))
print('truncated hash   ->', verify_password('password', h[:20]))
"
correct password -> True
wrong password   -> False
malformed hash   -> False
truncated hash   -> False
```

Full file, step 5 of the test plan:

```
$ .venv/bin/python -m pytest tests/unit/test_security.py -q
26 passed, 1 warning in 8.09s
```

## Eval iterations

**Run history**

Two runs, in order.

1. `6/6` — a partial `--only pkg-01,pkg-04,pkg-06,pkg-10,pkg-14,pkg-20`
   smoke run: one package from each of the five composition categories,
   plus both `thread-convention` packages, since that category has only two
   members and is the one the eval README names as the live floor risk.
2. `20/20` — the confirming full run, written to `eval-run.txt` with
   `--save-run`. Its agreement line reads
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` and its category
   line reads `clear-accept 7/7  scope-creep 4/4  thread-convention 2/2
   unbuildable 3/3  wrong-cause 4/4`.

The full run agreed on every package, so there was no disagreement to
revise and no second full run to pay for.

**Package analysis**

`pkg-16` (pandas-dev/pandas#57666, the pyarrow CSV engine stripping leading
zeros under `dtype=str`).

- **Gold label: `reject`**, category `wrong-cause`.
- **My rubric decided: `reject`**, on `cause-grounded`. They agree.

This package is the hardest of the four `wrong-cause` ones, because the
plan is excellent everywhere else: it names a real file and function
(`ArrowParserWrapper._finalize_pandas_output`), bounds its scope tightly,
proposes a concrete change, and ships a test plan. A rubric reading for
plan quality in general would accept it.

What sinks it is one line in the repro evidence. Step 4 of the reproduction
reads: *"pyarrow native with no `column_types` (inference on): the table's
column arrives as `int64` with value `1`; the zeros are already gone in the
parsed table, before any cast to string could run."* The plan's diagnosis
says the opposite — *"The defect is in the pandas-side cast that runs after
pyarrow returns its table… The cast is where the data is damaged, so the
cast is what must change."* The evidence pins the loss at the parse; the
plan blames the cast. Worse, the proposed fix follows from the wrong cause
into something actively bad: re-padding integers back out to a guessed
original width, reconstructing data that was destroyed upstream instead of
not destroying it. The thread's MEMBER had already explained the real
mechanism, and another commenter had sketched the real fix (map `dtype`
into pyarrow's `column_types` so the column parses as string in the first
place).

My rubric caught it because `cause-grounded` is written as a comparison
against the repro evidence's controls rather than as a judgement about
whether the diagnosis sounds reasonable — and specifically because the
check names the shape this package has: *"when the evidence pins the
failure at a different stage than the cause names."* Step 4 of that
reproduction exists precisely to pin the stage, and the check sends the
grader to it.

**Check rationale**

From the `rubric.md` uploaded to `tools/plan-check/`, the `cause-grounded`
check, exactly as it reads now:

> | `cause-grounded` | The plan's stated cause, read against what the repro-evidence block actually shows — its steps, its artifacts, and especially its control runs. | The stated cause explains the behavior the repro evidence shows, and nothing in that evidence rules it out. Fails when a control run contradicts the named cause (the plan blames a component the control proves is working), when the evidence pins the failure at a different stage than the cause names, or when the plan calls a signal the evidence establishes "a red herring" without new evidence of its own. A cause that merely goes further than the evidence proves, and says so, does not fail here — that is an unknown, not a contradiction. | required |

Three things in that wording are deliberate, and two of them are there
because of specific packages.

First, the evidence column points at the **control runs**, not at the repro
evidence generally. In four of the twenty packages the deciding fact is a
control: `pkg-11` blames yq's collect operator while the control shows
collect producing `[null]` correctly at top level; `pkg-07` claims the
friendly-error system is absent from the bundle while the control shows an
instance-method misuse printing a friendly error from that same build. A
check that said "read the diagnosis against the reproduction" would let a
grader skim the steps and miss the one line that decides it. So the
procedure's read order makes the grader write the controls down *before*
reading the plan, and this check sends them straight there.

Second, the three named failure shapes — contradicted by a control, wrong
stage, and a dismissed signal — are an enumeration rather than a
description, because "the diagnosis is not supported by the evidence" is
exactly the kind of adjective-shaped rule that makes two graders disagree.
The third shape, *"calls a signal the evidence establishes 'a red herring'
without new evidence of its own"*, exists only because `pkg-01` does that
in those words.

Third, and most important, the last sentence is a carve-out rather than a
tightening: *"A cause that merely goes further than the evidence proves,
and says so, does not fail here — that is an unknown, not a contradiction."*
Without it, the check would reject good plans. `pkg-13` says *"I have not
yet verified which layer clamps the viewport, so the exact fix site within
the branch may move one level during implementation"*, and `pkg-14` names
its exact functions as still to be pinned during tracing. Both are gold
`accept`. Reaching past your evidence honestly is what a plan is; only
contradicting it is the failure.

**Trade-offs**

The carve-out is what this check gives up. By letting a plan reach past its
evidence as long as it labels the reach, `cause-grounded` cannot catch a
confident-but-wrong diagnosis that is merely *unsupported* rather than
*contradicted*. If a package named a plausible cause that no control
touched and no evidence located, and hedged it with "I haven't confirmed
this yet", my rubric would pass it and let the author go build from a
guess. I accept that, because the alternative — failing any cause the
evidence does not positively prove — would have rejected `pkg-13` and
`pkg-14`, two of the seven clear-accepts, and cost more than it saved.

It also means this check does no work at all on a package whose repro
evidence has no controls. All twenty here have them, so the risk never
materialised in this set; on a real issue with a thinner reproduction, the
check degrades to reading the stage the evidence pins, and if the evidence
pins nothing, it passes by default.

Nothing changed elsewhere when I wrote it that way, and here is how I know:
I never revised this check. The smoke run graded one package from each
category, including both `thread-convention` packages, and returned `6/6`;
the confirming full run returned `20/20` with every category matched. There
was no disagreement to chase, so there was no loosening, and therefore no
canary re-run to report. The check as quoted is the first and only version
that ran.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
