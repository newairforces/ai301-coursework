# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

newairforces

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5850716627

````
I'd like to pick this one up as my first contribution here.

I'm a CS student working mostly in Python, so a small, well-bounded fix in
`core/security.py` is about the right size for me to do properly.

What I understand the issue to be: `verify_password` hands the stored hash
straight to `pwd_context.verify()`, and passlib raises `UnknownHashError`
when the stored value isn't a hash it recognises, so verification against a
malformed hash blows up instead of failing closed and returning `False`.
The covering test, `test_verify_with_wrong_hash_format` in
`tests/unit/test_security.py`, is marked `xfail(strict=True)` against
manifest id H-05, so the marker comes off as part of the fix.

What I'm doing next: reproducing it locally against a clean checkout of
`main`, confirming that the exception is what escapes and that a valid hash
still verifies correctly, and then posting a full reproduction report on
this thread with the output. Once that's up I'll look at where the
exception should be caught in `verify_password` and what the right failure
behaviour is.

I'll follow up here with the report rather than opening anything before
that. I can see other people have already commented on this issue — I'm not
looking to take it away from anyone, just working through it myself.
````

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72#issuecomment-5850717106

````
Reproduced. `verify_password` raises `passlib.exc.UnknownHashError` instead
of returning `False` when the stored hash isn't a recognisable format.

**Environment**

- Repo at commit `f89c06f` on `main`, fresh clone of my fork
- Python 3.14.7, pytest 9.1.1
- passlib 1.7.4, bcrypt 4.3.0
- macOS 26.6.2 (arm64)

Two honest notes about that environment. `pyproject.toml` declares
`requires-python >=3.11` and I ran 3.14, which is newer than anything the
project pins, so it is possible some of this is version-specific — though
the raise comes from passlib's hash identification and doesn't look
Python-version dependent to me. And passlib 1.7.4 prints
`(trapped) error reading bcrypt version` against bcrypt 4.x on import; that
is a known passlib/bcrypt packaging mismatch, unrelated to this issue, and
I've left it out of the output below where it appeared.

**Steps and output**

The test that covers this is marked `xfail(strict=True)`, so a normal run
reports it as expected-failure and hides the exception:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q
1 xfailed, 1 warning in 0.23s
```

Running the same test with `--runxfail` forces it to execute for real:

```
$ .venv/bin/python -m pytest \
    tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -q --runxfail
>           raise exc.UnknownHashError("hash could not be identified")
E           passlib.exc.UnknownHashError: hash could not be identified

.venv/lib/python3.14/site-packages/passlib/context.py:1132: UnknownHashError
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
1 failed, 1 warning in 0.18s
```

Calling the underlying context directly, with the same malformed value the
test uses, shows where it comes from:

```
$ .venv/bin/python -c "
from passlib.context import CryptContext
pwd_context = CryptContext(schemes=['bcrypt'], deprecated='auto')
print(pwd_context.verify('password', 'not_a_valid_bcrypt_hash'))
"
Traceback (most recent call last):
  File "<string>", line 4, in <module>
    print(pwd_context.verify('password', 'not_a_valid_bcrypt_hash'))
  File ".../passlib/context.py", line 2343, in verify
    record = self._get_or_identify_record(hash, scheme, category)
  File ".../passlib/context.py", line 2031, in _get_or_identify_record
    return self._identify_record(hash, category)
  File ".../passlib/context.py", line 1132, in identify_record
    raise exc.UnknownHashError("hash could not be identified")
passlib.exc.UnknownHashError: hash could not be identified
```

**Control**

A well-formed hash still behaves correctly, so this is specific to
unrecognisable stored values and not a general breakage of verification:

```
$ .venv/bin/python -c "
from passlib.context import CryptContext
c = CryptContext(schemes=['bcrypt'], deprecated='auto')
h = c.hash('password')
print('correct password ->', c.verify('password', h))
print('wrong password   ->', c.verify('nope', h))
"
correct password -> True
wrong password   -> False
```

**Expected:** `verify_password("password", "not_a_valid_bcrypt_hash")`
returns `False` — verification against a malformed stored hash fails
closed.

**Actual:** it raises `passlib.exc.UnknownHashError: hash could not be
identified`, which propagates out of `verify_password` to the caller. The
control above shows valid hashes still return `True`/`False` as expected.

One thing I have **not** established: where the malformed hash would come
from in practice, or whether any current code path can reach
`verify_password` with one. I only showed that the function raises when
handed one. That seems worth knowing before deciding whether this is a
defensive fix or a reachable bug, and I haven't looked yet.
````

## Eval iterations

**Run history**

Four runs, in order. The first three were partial `--only` runs; the fourth is the
full run in `eval-run.txt`.

1. `5/6` — `--only pkg-01,pkg-02,pkg-06,pkg-09,pkg-16,pkg-20`, one package from each
   composition category, to shake out the wording before paying for a full run. The
   miss was `pkg-09`.
2. `5/6` — `--only pkg-09,pkg-10,pkg-02,pkg-17,pkg-20,pkg-15` after loosening
   `claims-backed`. `pkg-09` and `pkg-10` both came right, but the canary caught a
   flip: `pkg-20`, the single `disclosure` package, went from reject to accept.
3. `6/6` — `--only pkg-20,pkg-03,pkg-07,pkg-09,pkg-12,pkg-16` after rewriting
   `repo-conventions`. Since that revision was a tightening, the canaries were the
   four accepts whose repos mention AI at all, to make sure the disclosure rule did
   not start firing on them.
4. `18/20` — the confirming full run, written to `eval-run.txt` with `--save-run`. Its
   agreement line reads `agreement: 18/20 scored items  (bar: 18/20: PASS)` and its
   category line reads `clear-accept 6/8  disclosure 1/1  no-evidence 4/4
   unfollowable-comms 3/3  wrong-target 4/4`.

**Package analysis**

`pkg-05` (conda/conda#16543, the `EnvironmentSectionNotValid` message breaking `--json`
output).

- **Gold label: `accept`.**
- **My rubric decided: `reject`**, on `steps-rerunnable`.

The report's steps line reads: *"wrote a minimal `env.yml` containing a valid
`dependencies:` list plus a `category:` section (the section conda does not
recognize), then:"* — and then pastes the command and its output. So the command is
literal and the artifact is real, but the input file is **described rather than
shown**. My `steps-rerunnable` check asks for "a literal command, input file, or
configuration the reader could obtain and run", and the grader read a described file
as not shown, so the check failed and the verdict flipped.

That reading is defensible against my wording and wrong against the package. The gold
label is right: `category:` plus a normal `dependencies:` list is a complete enough
description that any conda user can rebuild the file in ten seconds, and the report's
own artifact proves the file existed and did what it says. What I was actually trying
to catch with that check is `pkg-18`, where the input is a private company monorepo
and an internal block list that the reader can never obtain at all. Those two are not
the same failure: one input is *unshown*, the other is *unobtainable*. My check
collapsed them into one condition and punished the harmless one.

The same root cause produced my other miss, `pkg-03`, where the control run
(*"Dropping `-r '$1'` from the same command reports 1, 4, 7, 10 correctly"*) is
stated in prose without its own pasted output and tripped `claims-backed`. Both misses
are my rules penalising secondary evidence that is described instead of pasted, while
the primary artifact is shown in both. The fix is to word both checks around whether
the reader could obtain the thing, not whether the author pasted it — I diagnosed it
but did not spend another full run on it, so the committed run carries both misses.

Worth recording honestly: `pkg-03` **passed** this same check in run 3, twenty minutes
before it failed in run 4, with no change to the rubric in between. That is the flaky
check from the lecture's stability slide — my wording leaves enough room that the
grader can decide it differently on two runs of the same input.

**Check rationale**

From the `rubric.md` uploaded to `tools/repro-check/`, the `repo-conventions` check,
exactly as it reads now:

> | `repo-conventions` | The repo-facts block: the bug-report template's stated asks, and the contribution policy including any AI-use policy, each read against the two comments. | The two comments give what this repo asks of them, on both surfaces. **Template:** if the template requires confirming the bug on a particular version — the latest release, the main branch — the report tests that version or says why it could not. **AI disclosure:** every package in this course is produced with AI assistance, so a disclosure requirement always applies to the author and is never waived for lack of proof that AI was used. If the policy requires disclosing AI assistance in issues or comments, the comments must carry a disclosure naming the tool and the extent of its help; comments with no disclosure line at all fail, however strong the reproduction beneath them. Two things are not disclosure requirements and pass without one: a policy that only asks disclosure in pull requests (or states no ask for issue comments), and a policy that requires comments be written by a human in their own words, which is satisfied by comments that read as the author's own. Silence passes — most repos state no AI policy. | required |

The long clause in the middle exists because of one canary flip. My first version said
only that "if the contribution policy requires disclosing AI assistance in issue
comments, the comments disclose it, naming the tool and the extent." That sounds
complete, and it graded `pkg-20` (ghostty, whose policy requires that *all AI usage in
any form must be disclosed*) as **accept** in run 2 — losing the entire `disclosure`
category, which contains exactly one package and therefore cannot be bought back on
volume.

The reason is worth stating, because it is not a wording slip. Nothing in `pkg-20`'s
text shows that its author used AI. The grader reasoned, reasonably, that a disclosure
requirement cannot be violated by someone who never used the tool, and passed the
check. The rule was asking the grader to infer a fact the package does not contain. So
I replaced the inference with a stated premise — *"every package in this course is
produced with AI assistance, so a disclosure requirement always applies to the author
and is never waived for lack of proof that AI was used"* — which turns the check into
a question about the policy's text rather than about the author's invisible workflow.

I also had to write the two exemptions explicitly, because tightening a disclosure rule
is exactly the move that starts failing good packages. `pkg-09` (fd) has a policy that
asks for disclosure in pull requests and *"states no disclosure ask for issue
comments"*; `pkg-03` (ripgrep) requires that comments be *"written by humans in their
own words"*, which is a voice requirement, not a disclosure one. Both are gold
`accept`, and a rule that merely searched for the word AI in the policy would have sunk
them.

**Trade-offs**

The premise is what the check gives up. By assuming every author is working
AI-assisted, the check can no longer be satisfied by a contributor who genuinely wrote
everything themselves in a repo that demands disclosure: my rubric will hold their
comment for a missing disclosure line they do not owe. Inside this course that premise
is simply true, so the cost is zero here; outside it, the check would need a way to
establish AI use rather than assume it, and I do not have one that reads from the text.
It also cannot judge whether a disclosure is *honest* — a one-line "AI helped with
grammar" over a wholly generated report passes, because the check reads for the
presence of a tool and an extent, not for whether the extent is true.

The canaries are how I know what it cost elsewhere. After tightening, I re-ran
`--only pkg-20,pkg-03,pkg-07,pkg-09,pkg-12,pkg-16`: `pkg-20` returned to `reject`, and
all four accepts whose repos mention AI (`pkg-03` ripgrep, `pkg-07` p5.js, `pkg-09` fd,
`pkg-12` prettier) stayed `accept`, for `6/6`. The full run then reproduced all six of
those verdicts, so the disclosure rule is firing on exactly one package and no accept
lost ground to it. Both of the misses in the committed run came from other checks.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
