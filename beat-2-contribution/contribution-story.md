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
**Pull Request:** [GitHub PR link, once opened]

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

I installed the scout over my unit 1 rubric and wrote three `gh search issues` queries, two of them scoped with `--owner` to the Python web, API, and data projects my fit profile names, because an unscoped `--language python` search came back full of week-old repos. My first scan accepted 0 of 5, all already claimed, and the cause was my ranking rather than the rubric: "good first issue first, newest first" let the unscoped query fill the top five with issues someone had grabbed days earlier. So I ranked my named projects first, switched the tie-break to oldest-first, and dropped `claimed` labels and classroom repos. A second scan showed those fixes working but still found 0 of 5, now because the label rule promoted years-old threads full of dead PRs, so I ranked `bug` above newcomer labels and added a "new standalone tool" fail clause to `scope-bounded` after litestar#1272 slipped past its listed clauses.

**Unit 6: installed a voice guard on everything I post, and gave Claude a memory file for openedx-platform**

I folded my three voice-guide copies into one file that the guard and all three skills read, added Upstream rules for claims, questions, and pings, and hooked the guard into Claude Code. The wiring test was stopped as intended, and my real claim went out through the hook as a logged `pass`. The guard itself never fought me; what fell short was a tool reading it: repro-check reported "no voice guide" because the guide is now a symlink it didn't follow, so its voice notes on my repro report were silent, and I'll point it at the canonical path. The memory file (`CLAUDE.local.md`, kept out of git with `.git/info/exclude`) records what cost me an hour to find: Open edX's tests need only one Mongo container and SQLite, not the full Tutor stack, plus the exact `uv` and `pytest -c cms/pytest.ini` commands, the files behind my bug, and the AI-policy lines I'm bound by.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

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

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

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

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
