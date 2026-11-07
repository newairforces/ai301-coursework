# Contribution: PUT course_details updates inconsistently (possibly requires start_date?)

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Kibur Assefa
**GitHub Username:** newairforces
**Issue:** https://github.com/openedx/openedx-platform/issues/39094
**Pull Request:** https://github.com/openedx/openedx-platform/pull/39328

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: turned my unit 1 issue-select skill into a scout that searches GitHub, then fixed the ranking twice**

I installed the scout over my unit 1 rubric and wrote three `gh search issues` queries, two of them scoped with `--owner` to the Python web, API, and data projects my fit profile names, because an unscoped `--language python` search came back full of week-old repos. My first scan accepted 0 of 5, all already claimed, and the cause was my ranking rather than the rubric: "good first issue first, newest first" let the unscoped query fill the top five with issues someone had grabbed days earlier. So I ranked my named projects first, switched the tie-break to oldest-first, and dropped `claimed` labels and classroom repos. A second scan's top five had no claimed issues left, which is how I knew those fixes worked, but it still accepted 0 of 5, now because the label rule promoted years-old threads full of dead PRs, so I ranked `bug` above newcomer labels and added a "new standalone tool" fail clause to `scope-bounded` after litestar#1272 slipped past its listed clauses.

**Unit 6: installed a voice guard on everything I post, and gave Claude a memory file for openedx-platform**

I folded my three voice-guide copies into one file that the guard and all three skills read, added Upstream rules for claims, questions, and pings, and hooked the guard into Claude Code. The wiring test was stopped as intended, and my real claim went out through the hook as a logged `pass`. The guard itself never fought me; what fell short was a tool reading it: repro-check reported "no voice guide" because the guide is now a symlink it didn't follow, so its voice notes on my repro report were silent, and I'll point it at the canonical path. The memory file (`CLAUDE.local.md`, kept out of git with `.git/info/exclude`) records what cost me an hour to find: Open edX's tests need only one Mongo container and SQLite, not the full Tutor stack, plus the exact `uv` and `pytest -c cms/pytest.ini` commands, the files behind my bug, and the AI-policy lines I'm bound by.

**Unit 7: installed the ship-gate in front of `gh pr create` and wrote a four-check manifest for openedx-platform**

My manifest runs four checks taken from `unit-tests.yml`, `pylint-checks.yml`, and my `CLAUDE.local.md`: start the Mongo test container, the two test files I touched, and pylint on the touched files. The full CI shards and module-wide pylint are in the excluded table with dated reasons. What fought me was the fresh-terminal rule: `docker start` exited 127 in a clean shell, so that row calls `/usr/local/bin/docker`. On my branch the gate passed all four checks and pr-precheck gave 6/6 required, but it failed the preferred `thread-engaged` check because my description ignored the maintainer's CLA reply, so I added that line and a pasted `git grep`, re-ran to 8/8, and the hook let PR #39328 through on that pass.

**Unit 8: added a PR monitor with a playbook built from openedx-platform's real response times**

I wrote my playbook from measured data: five recent merged outside PRs got a first review in 0 to 4 days, but all from authors who already had CLAs, so my threshold is 10 days after the CLA turns green, and an "admin blockers first" rule keeps the clock stopped while `openedx/cla` is red. The monitor read my PR as FEEDBACK-IN (a community-team comment about the CLA, not a code review) and drafted a reply asking them to re-check my signature, but it left an `<email used on the form>` placeholder and assumed facts only I could confirm. I confirmed I'd signed as `newairforces` and had the confirmation email, then rewrote the reply without my email, since the thread is public. My voice guard stopped it once for a missing AI disclosure the draft never had, so I added one, and it posted on the second try.

---

## Why I Chose This Issue

I started with my scout's own scans of GitHub, and neither turned up anything I could take: the first two runs graded ten issues and accepted none, because every one was already claimed or had dead PRs behind it. So my real pick came from the AI301 Issue Selection site. Its Organization list didn't include the Python web projects my strategy targets (Flask, FastAPI, Pydantic and the like), and its Python filter matched any repo with a little Python in it, so page one was mostly C++ and Fortran. Filtering to `openedx` with the `bug` tag gave me a page of Django REST API bugs in `openedx-platform`. A free `gh` pass dropped four that already had open or closed PRs, and my scout graded the remaining three. All three were accepted, and after this week's scope patch it rated this one, #39094, **moderate**: the location is named, but one open edge needs the maintainers (should a pacing change on a course that has already started be refused with a 400 instead of a silent 200?). I asked that question in my claim. The issue fits my profile exactly: it is one request path, `PUT /api/contentstore/v1/course_details/<key>` into `CourseDetails.update_from_json`, with a reproduction I can run by sending two request bodies, and a wrong result I can assert in a test. I want to learn how a large Django codebase layers a REST view over its storage, and how to work with maintainers who owe me nothing.

Honestly, the scout's searching was weaker than its grading. Its first two scans failed because of my ranking rules, not the rubric, and I fixed them twice. Its grading of the openedx issues held up when I checked by hand, with one real miss: it called Open edX's AI policy "silent" because it only looked inside the repo, while the actual policy lives org-wide in `openedx/.github/AI_POLICY.md` (it allows Claude and requires disclosure). The verdict happened to be right, but the same miss on a project that bans AI would have been a false accept, so I patched the rubric to read the org's `.github` repo. It also kept grading `maintainer-responsive` as unclear because Open edX hides org membership and answers through merged PRs rather than comments; I left that, since it is a ranking check and never cost a verdict.

Claim comment: https://github.com/openedx/openedx-platform/issues/39094#issuecomment-6390444120

**Toolkit**

- Tool: issue-scout, my unit 1 rubric grown a search strategy (three `gh search issues` queries scoped to Python web/API/data projects, plus ranking rules). I used it for two scans, then in direct mode to grade three picks from the Issue Selection site, twice.
- What it produced: two scans with 0 of 5 accepted each, then accept on all three openedx picks; after the scope patch, #39094 came back accept with a **moderate** difficulty note and the open question about already-started courses.
- Difficulties: my ranking surfaced issues that were already claimed (twice, for two different reasons); the `ai-policy-permits` evidence missed Open edX's org-wide AI policy; `maintainer-responsive` can't see a project that responds through merged PRs instead of badge-marked comments; and the old `scope-bounded` check could reject honest issues with a single open edge.
- What changed: reworked the ranking rules twice (named projects first, oldest-first tie-break, `bug` above newcomer labels, drop `claimed` labels and classroom repos); made `ai-policy-permits` read the org's `.github` repo; and applied the unit 6 scope patch, replacing my unit 1 `scope-bounded` check with `scope-feasible` plus a difficulty note, so an open edge now becomes my first question to the maintainer instead of a rejection.

---

## Understanding the Issue

**Problem Description**

Studio's course details API says you can send only the fields you want to change, but it doesn't really work that way. If an API client sends `{"self_paced": true}` to switch a future course to self-paced, the request answers 200 and nothing changes. The Studio MFE never sees this because it always sends the whole form, so this bug only hits other API clients.

**Expected Behavior**

A partial `PUT` changes the fields in the body and leaves every other field alone. `{"self_paced": true}` on a course that hasn't started yet should make it self-paced, keep its start and end dates, and return the new value.

**Current Behavior**

The pacing change is silently dropped (HTTP 200, `self_paced` still `false`), and worse, the course's stored start date, end date, and enrollment start and end dates are all set to `None`, because any date field missing from the body is written as "no date". I confirmed that on `master` with my repro test.

**Affected Components**

- `openedx/core/djangoapps/models/course_details.py`, `CourseDetails.update_from_json`: the five date blocks (`start_date`, `end_date`, `enrollment_start`, `enrollment_end`, `certificate_available_date`), each with `else: converted = None`.
- `xmodule/course_block.py`, `CourseBlock.can_toggle_course_pacing`: refuses a pacing change when there is no start date, which is why the wiped start date blocks `self_paced`.
- `cms/djangoapps/contentstore/utils.py`, `update_course_details`: sits between the view and the model, and resets the certificate fields whenever `self_paced` is true.
- `cms/djangoapps/contentstore/rest_api/v1/views/course_details.py`: the `PUT` view whose docstring promises partial updates.

**Toolkit**

- Tool: Claude Code inside my clone, with my `CLAUDE.local.md` loaded, used to trace the request from the view to the model, plus my local repro test to check each step against real output.
- What it produced: the call chain above and one new fact: a fourth repro case (2026-10-31) showed a partial body also wipes `end`, `enrollment_start`, and `enrollment_end`, not just `start` as I'd guessed in my claim.
- Difficulties: Claude's first explanation stopped at `start_date`, because that was the only field in my claim. I had to ask it to check the other four date blocks, which turned out to have the same bug.
- What changed: I added the `end_date` and enrollment-date case to my local repro test before writing the plan, so the plan's scope is based on output and not on a guess.

---

## Reproduction Process

**Environment Setup**

macOS 27.0.1 on Apple silicon; openedx-platform `master` at `87cd3811a4` (2026-10-23), cloned from my fork `newairforces/openedx-platform`; Python 3.12.15 (the repo requires `>=3.12`), Django 5.2.18. I installed the test dependencies from the repo's own lockfile with `uv sync --no-default-groups --group testing --frozen --python 3.12`, which is what `make test-requirements` runs. The tests need MongoDB, so I ran a single `mongo:7` container (`docker run -d --name edx-test-mongo -p 27017:27017 mongo:7`); the SQL side is SQLite, as `cms.envs.test` configures.

Challenges: the official dev setup is Tutor, a full Docker stack that would have eaten a large share of my time budget. Reading `cms/envs/test.py` showed the unit tests only need Mongo on localhost plus SQLite, so one container replaced the whole stack. Building the dependencies needed `pkg-config` and `libxmlsec1` from Homebrew, plus `PKG_CONFIG_PATH` pointed at Homebrew's `mysql-client` so `mysqlclient` would compile. To prove the environment worked before trusting it, I ran the existing tests for this view first: `40 passed`. The trade-off is that I reproduce through Django's test client against the real view, not through the authoring MFE or a running server.

**Steps to Reproduce**

1. Control: `.venv/bin/pytest -c cms/pytest.ini cms/djangoapps/contentstore/rest_api/v1/views/tests/test_course_details.py -q` gives `40 passed`.
2. Add a local, uncommitted test (`test_repro_39094.py`, kept out of git with `.git/info/exclude`) that uses the repo's `CourseTestCase`. It sets a course to instructor-paced with `start = 2040-01-01`, then sends `PUT /api/contentstore/v1/course_details/<course_id>` with `{"self_paced": true}` and with `{"self_paced": "true"}`. On a fresh course it sends `{"self_paced": true, "start_date": "2040-01-01T00:00:00Z"}`, and on a course that started 30 days ago it sends the same body with that past start date. After each PUT it prints the status, a follow-up GET, and the stored block read back from the modulestore.
3. Observed result: the partial body answers **HTTP 200** with `self_paced=False`, and the GET agrees, so the change is silently dropped. The same body plus the course's own future `start_date` works (`self_paced=True`). A course that has already started refuses the change but still answers 200. On top of that, the partial body **cleared the stored start date**: `block.start` went from `2040-01-01` to `None`.

**Reproduction Evidence**

- **Commit showing reproduction:** none. The repro test is deliberately local and uncommitted (excluded via `.git/info/exclude`) so it can never ride along into my PR; its full source is in my repro report.
- **Screenshots/logs:**

```
[setup] instructor-paced course, start=2040-01-01T00:00:00+00:00
PUT {"self_paced": true}
  -> HTTP 200, response self_paced=False
  -> GET  self_paced=False start_date=None certificates_display_behavior=CertificatesDisplayBehaviors.EARLY_NO_INFO
  -> stored block.start=None block.self_paced=False
[setup] instructor-paced course, start=2040-01-01T00:00:00+00:00
PUT {"self_paced": true, "start_date": "2040-01-01T00:00:00Z"}
  -> HTTP 200, response self_paced=True
  -> stored block.start=2040-01-01 00:00:00+00:00 block.self_paced=True
[setup] instructor-paced course, start=2026-09-24T22:47:49 (already started)
PUT {"self_paced": true, "start_date": "2026-09-24T22:47:49Z"}
  -> HTTP 200, response self_paced=False
3 passed, 22 warnings in 2.89s
```

- **My findings:** the issue is real on today's `master`, in all three cases it describes. I also found something it doesn't mention: a partial body wipes the stored `start_date`, because `update_from_json` writes `None` into `block.start` whenever `start_date` is absent from the body. That same `None` is why `can_toggle_course_pacing` returns `False` and the `self_paced` change is skipped. So the two symptoms have one cause. `end_date` uses the same pattern and probably has the same problem, but I haven't tested it yet. I didn't print `certificates_display_behavior` before each PUT, so this run neither confirms nor rules out the issue's certificate side effect.

**Toolkit**

- Tool: repro-check, my unit 2 skill, now with its real-repo scope; I graded my claim draft alone, then the claim and repro report together.
- What it produced: the claim draft alone got accept. Claim plus report first got reject, failing `steps-rerunnable` (my repro test was described in prose, so a stranger couldn't re-run it), `repo-conventions` (no AI disclosure on the report, which Open edX's AI policy asks for), and `claim-is-bounded` (my claim says "within about a week"). After fixes, accept on all eight checks.
- Difficulties: `claim-is-bounded` contradicted itself between runs. It passed the same claim alone, then failed it alongside the report, because my unit 2 wording failed any "deadline or date", even a timeframe for reporting back, which this unit's claim step explicitly asks for. It also told me there was no voice guide, because the guide is now a symlink into `~/.claude/voice-guard/` and the grader didn't follow it.
- What changed: I pasted the full repro test into the report in a collapsible block, added an AI-disclosure line, and stopped presenting the certificate value as evidence. I also narrowed `claim-is-bounded` so it fails only a deadline or date for a fix or PR, and passes a rough timeframe for reporting findings back.

---

## Solution Approach

**Analysis**

`PUT /api/contentstore/v1/course_details/<course_id>` (`cms/djangoapps/contentstore/rest_api/v1/views/course_details.py`) passes the request body to `update_course_details` in `cms/djangoapps/contentstore/utils.py`, which calls `CourseDetails.update_from_json` in `openedx/core/djangoapps/models/course_details.py`. That method handles each of the five date fields the same way:

```python
if 'start_date' in jsondict:
    converted = date.from_json(jsondict['start_date'])
else:
    converted = None
if converted != block.start:
    dirty = True
    block.start = converted
```

So when a key is missing from the body, the field gets set to `None`, the same as if the client had sent `null`. Later in the same method, the `self_paced` branch only runs if `block.can_toggle_course_pacing` is true, and that check (`xmodule/course_block.py`) returns `False` when `block.start` is empty. So for `{"self_paced": true}`, the start date is cleared first, which makes the pacing check fail, and the view still answers 200 with the old value. Both symptoms in my repro (pacing ignored, dates wiped) come from that `else: converted = None`. The issue says the Studio MFE always sends the whole form (the old Backbone settings page is no longer in the repo), which is why the bug only shows up for other API clients.

**Proposed Solution**

When a date key is missing from the body, keep the course's current value instead of writing `None`. In each of the five `else:` branches I'll change `converted = None` to the block's current value (`block.start`, `block.end`, `block.enrollment_start`, `block.enrollment_end`, `block.certificate_available_date`). That's five one-line changes. An explicit `null` or `""` still clears a date, so the MFE's full-form saves behave exactly as before, and `update_course_details` setting `certificate_available_date` to `None` for self-paced courses still works.

What I'm deliberately not changing:
- The refusal to change pacing on a course that has already started. `can_toggle_course_pacing` refuses that on purpose. Whether it should answer 400 instead of 200 is the question I asked in my claim, and it hasn't been answered yet, so it stays out of this PR.
- The certificate reset in `update_course_details` still runs even when the pacing change is refused (only on already-started courses after this fix). That belongs with the 400 question.
- Other non-date fields in `update_from_json`, the view, the serializer, and the MFE.

**Implementation Plan**

1. Create branch `fix/39094-partial-put-keeps-dates` from `master`.
2. In `openedx/core/djangoapps/models/course_details.py`, `update_from_json`: change the five `else: converted = None` lines so they keep the block's current date. Add one short comment saying why (a key left out of the body means "unchanged").
3. In `openedx/core/djangoapps/models/tests/test_course_details.py`, add a model test: a course with all four schedule dates set, then `update_from_json` with a body that has none of the date keys. All four dates should be unchanged. Also check that sending `None` for a key still clears it.
4. In `cms/djangoapps/contentstore/rest_api/v1/views/tests/test_course_details.py`, add a view test with the issue's own request: an instructor-paced course starting in 2040, `PUT {"self_paced": true}`. Expect 200, `self_paced` true in the response and the stored course, and the start date unchanged.
5. Run both new tests on `master` without the fix and confirm they fail, then with the fix and confirm they pass. Re-run my local repro test, then run my manifest's checks (both test files and pylint on the touched files).
6. Commit using Conventional Commits (`fix: ...`, `test: ...`), with a `Co-authored-by` trailer, as the Open edX AI policy asks.

Unknowns: I can't run the full CMS test shards here (they need CI's MySQL and other services), so other callers of `update_course_details` are only covered once CI runs. If something in there depends on a missing key clearing a date, CI is where it'll show up.

Deviations: none yet.

**Toolkit**

- Tool: plan-check, my unit 3 skill with the real-repo scope, grading `plan.md` (this section) and a draft plan comment together.
- What it produced: accept, 5/5 required checks (cause-grounded, scope-bounded, executable, test-plan-decisive, thread-aligned). It failed the preferred `unknowns-stated` check because every step read as settled, and its voice notes caught a result I described without pasting, plus a line ("happy to handle it in a follow-up") that came close to promising a PR.
- Difficulties: the first run graded nothing. Run as `claude -p` from my clone, it couldn't read its own files in `~/.claude/skills/`, and it returned a placeholder reject instead of guessing, which is the right failure. It also flagged a stale note saying my CLA wasn't signed: my `CLAUDE.local.md` was a week out of date.
- What changed: I re-ran it with `--add-dir ~/.claude`, added the Unknowns line above, pasted the repro output into the comment, softened the follow-up line, and updated the CLA line in `CLAUDE.local.md`. The procedure also has a gap it named itself: no rule for grading `cause-grounded` when the repro was never posted on the thread.

---

## Testing Strategy

Three layers, each with output I kept:

1. **The two new tests fail without the fix.** I wrote them before touching `course_details.py` and ran them on the unfixed code:
   ```
   E       assert False is True
   FAILED .../views/tests/test_course_details.py::CourseDetailsViewTest::test_put_partial_body_self_paced
   E               AssertionError: start_date
   E               assert None == datetime.datetime(2040, 1, 1, 0, 0, tzinfo=zoneinfo.ZoneInfo(key='UTC'))
   FAILED .../models/tests/test_course_details.py::CourseDetailsTestCase::test_partial_update_keeps_dates
   ```
   The view test sends the issue's own request, `PUT {"self_paced": true}`, to a course starting in 2040. The model test sends a body with no date keys and checks that all four schedule dates survive, then sends `{"end_date": None}` to prove an explicit null still clears a date.
2. **My reproduction, re-run before and after.** The same local repro test from Reproduction Process, plus a fourth case with end and enrollment dates set:
   ```
   before:  -> HTTP 200, response self_paced=False
            -> stored block.start=None block.self_paced=False
            -> stored end=None enrollment_start=None enrollment_end=None
   after:   -> HTTP 200, response self_paced=True
            -> stored block.start=2040-01-01 00:00:00+00:00 block.self_paced=True
            -> stored end=2041-01-01 00:00:00+00:00 enrollment_start=2039-06-01 00:00:00+00:00 enrollment_end=2039-12-01 00:00:00+00:00
   ```
   The already-started case still answers 200 with `self_paced=False` after the fix, which is the behavior I left alone on purpose.
3. **The repo's own checks on the branch:** view tests `41 passed` (40 before, plus mine), model tests `27 passed` (26 plus mine), pylint on the three touched files clean, and, as an extra check outside the manifest, the older `cms/djangoapps/contentstore/tests/test_course_settings.py`, which also goes through `update_from_json`: `81 passed`. Not run: the full CI shards and module-wide pylint (they need CI services and the dev group). CI covers those once a maintainer starts it.

**Toolkit**

- Tool: the ship-gate, running my manifest's four checks in a clean shell and then my pr-precheck against `test_evidence.md` (the before and after outputs above, plus the check output).
- What it produced: all four checks `ok` (Mongo 0s, view tests 21s, model tests 14s, pylint 15s), and pr-precheck passed `repro-rerun-decisive` and `repo-checks-run`, quoting the before and after `self_paced` and `block.start` lines as its evidence.
- Difficulties: the clean shell is not my terminal. `docker` isn't on the bare PATH, so the Mongo row exited 127 when I tested it by hand, and I only found out because I tested every row in a clean `env -i` shell before trusting the manifest.
- What changed: the Mongo row calls `/usr/local/bin/docker` directly. I also ran the wider `test_course_settings.py`, which isn't in the manifest, to cover the plan's stated unknown as far as I could locally.

---

## Implementation Notes

**2026-10-31, morning: scope grew from one date to five**

Before planning, I added a fourth case to my local repro to test my claim's guess about `end_date`. It was worse than I guessed: `{"self_paced": true}` also wiped `end`, `enrollment_start`, and `enrollment_end`. So the plan covers all five `else: converted = None` branches in `update_from_json`, not just `start_date`. I considered fixing only `start_date`, since that alone fixes the issue's symptom, but the other four are the same one-line bug a line apart, and a reviewer would fairly ask why I left them.

**2026-10-31: tests first, then the fix**

On branch `fix/39094-partial-put-keeps-dates`, I wrote the view test and the model test and ran them on the unfixed code, which gave two failures (output in Testing Strategy). Then I changed the five `else` branches to `converted = block.<field>`, with one comment saying a missing key means "unchanged". I checked `Date().from_json("")` and `from_json(None)` first: both return `None`, so an explicit `null` or `""` from the MFE still clears a date. One wrong turn: my plan first said the old Backbone settings page also sends every key, but `git grep` showed that JS isn't in the repo anymore, so I cut that claim.

**2026-10-31: commit identity**

Before committing, I found the clone would commit as `KPROsoft21`, my machine's global git identity, not the `newairforces` account that signed the CLA. I set a repo-local `user.email` to the `newairforces` noreply address, so the CLA check can match the commits. Two commits, Conventional Commits style (`fix:` for the code, `test:` for the tests), each with the `Co-authored-by: Claude` trailer that Open edX's AI policy asks for.

**2026-10-31: PR opened**

The ship-gate passed twice: first with the preferred `thread-engaged` failing, then 8/8 after I added the CLA line and pasted a `git grep` of the callers. I pushed to my fork, set `gh repo set-default openedx/openedx-platform`, and opened #39328 through Claude Code. The hook honored the manual pass and the voice guard logged `pass`. Then I posted the follow-up my claim promised on the issue, linking the PR. Deviations from the plan: none in the code. The plan's six steps happened as written.

**2026-11-07: CLA blocker instead of review**

The PR monitor's `--state` showed one failing check, and it turned out to be `openedx/cla`, not a test: CI can't start until it's green. @mphilbrick211 had commented on 2026-11-04 asking for the CLA form. I thought I'd already signed it on 2026-10-24 and replied saying so, but that form had only requested the agreement. The real one arrived by DocuSign the same day, so I signed it and posted a correction asking for the check to be re-run. There were no code changes, so the branch is unchanged at `67c37d8732`.

**Toolkit**

- Tool: Claude Code in my clone, with `CLAUDE.local.md`, for the edits, the test runs, and the commits, and my local repro test as the before and after measuring stick.
- What it produced: a 5-line fix plus 2 tests (53 insertions, 5 deletions across 3 files), two commits, and the saved before and after output that became `test_evidence.md`.
- Difficulties: the global git identity was the wrong account, which nothing warned me about, and the voice guard stopped my follow-up comment twice, once for a diagnosis written as an observation and once for "both fail without the fix" with no output.
- What changed: a repo-local git identity for this clone, and I split the comment's observation from its diagnosis and pasted the failing test output before it posted.

---

## Pull Request

**PR Link:** https://github.com/openedx/openedx-platform/pull/39328

**PR Description:** Title: `fix: keep course dates that a partial course_details PUT leaves out`. It follows the repo's PR template. The **Description** says `update_from_json` wrote `None` into every date field missing from the body, so `{"self_paced": true}` cleared the start date, `can_toggle_course_pacing` then refused, and the view answered 200 anyway. The fix keeps the stored value when a key is missing, and an explicit `null` or `""` still clears. Roles affected: Developer, and Course Author indirectly. It names what it doesn't change: the refusal for already-started courses (the open 400 question from my claim) and the certificate reset that goes with it. **Supporting information** has `Fixes #39094`, a link to my claim, and a note that I've signed the CLA. **Testing instructions** give the two pytest commands, the two new tests failing on unfixed code, my repro's before and after, and every check I ran with its output, plus what I didn't run and why. **Other information** pastes a `git grep` of the callers to show the one risk (a client relying on a missing key to clear a date) and ends with the AI disclosure: Claude helped trace the code, write the tests and the repro, and draft the description, and the commits carry a `Co-authored-by` trailer.

**Maintainer Feedback:**

- 2026-10-31: the `openedx-webhooks` bot added the `open-source-contribution` label and its checklist: product review if needed, context, CLA, and a green build. The CLA item still shows unsigned, and the bot says a signature can take a business day to process. Before that, on the issue, @bmtcril had asked me to sign the CLA before any PR could be approved or tested. I answered in the PR description and in my follow-up comment (https://github.com/openedx/openedx-platform/issues/39094#issuecomment-6549065873).
- 2026-11-04: @mphilbrick211 (Open edX community team) welcomed the PR and said the `openedx/cla` check would turn green once a CLA form was on file, with a link to the individual form.
- 2026-11-07: I replied that I'd signed the individual form on 2026-10-24 as `newairforces` and received the confirmation email. I pasted the still-red check status and asked whether it was recorded under that username or I need to sign again (https://github.com/openedx/openedx-platform/pull/39328#issuecomment-6712982218). No code review yet: CI and review wait on the CLA check.
- 2026-11-07: Michelle Philbrick (@mphilbrick211) then sent the actual agreement by DocuSign. The form I'd filled out on 2026-10-24 had only requested it, so my earlier comment was wrong. I signed it the same day and posted a correction asking for the check to be re-run (https://github.com/openedx/openedx-platform/pull/39328#issuecomment-6713065536).

**Status:** Awaiting review

**Toolkit**

- Tool: the ship-gate (`check_gate.py`), by hand on my branch and then as the `PreToolUse` hook on `gh pr create`, together with my unit 6 voice guard.
- What it produced: two hand runs, both `ship-gate: PASS`. The first had pr-precheck at 6/6 required with the preferred `thread-engaged` failing. The second, after my fixes, was 8/8. The hook then logged `PASS (honored the manual pass at 16:56)` without re-running anything, and the voice guard logged `pass` on the PR body.
- Difficulties: none blocking at the gate itself. The install test was confusing, though: Claude reported only the ship-gate's stop, so I had to feed the same payload to `check_voice.py` by hand to see that the voice guard also stopped it, as the two-stop test expects.
- What changed: the PR description gained the CLA line and the pasted `git grep` because of the first run's notes. Every edit invalidates the 30-minute pass, so I re-ran the gate instead of opening on the stale one.

---

## Learnings and Reflections

**Technical Skills Gained**

- How Studio's course details API reaches storage in Open edX: the DRF view in `cms/djangoapps/contentstore/rest_api/v1/views/course_details.py`, then `update_course_details` in `cms/djangoapps/contentstore/utils.py`, then `CourseDetails.update_from_json`, then the modulestore, and how `CourseBlock.can_toggle_course_pacing` sits in the middle of that.
- That "absent" and "null" are different things in a partial-update API, and that collapsing them into `None` silently destroys data. That one `else: converted = None` was both symptoms of my bug.
- Running a giant Django project's tests without its official dev stack: one `mongo:7` container plus SQLite (from reading `cms/envs/test.py`), `uv sync --group testing --frozen`, and the right `pytest -c cms/pytest.ini` config for CMS versus LMS paths.
- Writing tests that fail first: I ran both new tests on the unfixed code and kept the failure output as evidence.
- Open edX process: Conventional Commits (OEP-51), the PR template, `Co-authored-by` trailers under their AI policy, and the CLA check that gates CI for first-time contributors.

**Challenges Overcome**

- **Finding an issue at all.** My scout's first two scans accepted 0 of 5 because of my ranking rules, not the rubric. I fixed the ranking twice (Unit 5 entry) and ended up picking from the Issue Selection site, with the scout grading the picks.
- **Setting up the environment.** Tutor would have eaten my week. I read the test settings, used one Mongo container, and proved the setup with the existing 40 tests before trusting it (Reproduction Process).
- **The bug was wider than the issue.** My repro showed the partial body also wipes the start date. A fourth case on 2026-10-31 showed it wipes end and enrollment dates too, so I widened the plan with evidence, not a guess (Implementation Notes).
- **My own tools pushing back.** repro-check rejected my first report as not rerunnable. plan-check couldn't read its own files in print mode. pr-precheck failed `thread-engaged` because I ignored the maintainer's CLA comment. The voice guard stopped three of my posts. Each stop named a real problem, and I fixed the draft instead of getting around the tool.
- **Wrong git identity.** The clone would have committed as my other GitHub account. I caught it before the first commit and set a repo-local identity, so the CLA check could match the commits.

**What I'd Do Differently Next Time**

- Confirm admin requirements end to end before opening the PR. I signed the CLA, but I never checked that the `openedx/cla` check could see it, so my PR has sat a week with CI blocked. Next time I'd open a tiny draft PR, or check the CLA status page, before the real one.
- Test the guess in the claim before posting it. My claim named only `start_date`, and one more repro case would have shown all four dates.
- Keep `CLAUDE.local.md` current. plan-check flagged that it still said "CLA not signed" a week after I'd signed.
- Give my skills a real-repo dry run earlier. Several rubric fixes (`claim-is-bounded`, the voice-guide symlink, `ai-policy-permits` reading the org's `.github` repo) only showed up when they met a real project.

---

## Resources Used

- The issue itself, with its control case ("adding the course's own `start_date` makes it work"), which pointed straight at the date handling: https://github.com/openedx/openedx-platform/issues/39094
- @bmtcril's reply on the issue, pointing me to Open edX's contributor onboarding and the CLA: https://docs.openedx.org/en/latest/developers/quickstarts/so_you_want_to_contribute.html
- Open edX's org-wide AI policy, which set my disclosure lines and commit trailers: https://github.com/openedx/.github/blob/master/AI_POLICY.md
- OEP-51 on Conventional Commits, for the commit and PR titles: https://open-edx-proposals.readthedocs.io/en/latest/oep-0051-bp-conventional-commits.html
- The repo's own `cms/envs/test.py` and `.github/workflows/unit-tests.yml`, which showed I needed only Mongo plus SQLite, and which pytest flags CI uses.
- @mphilbrick211's comment on my PR, which showed me the CLA check was still the blocker: https://github.com/openedx/openedx-platform/pull/39328
