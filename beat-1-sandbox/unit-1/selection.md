# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72

**Verdict output**

Live-mode run of the installed skill over three candidates (#72, #68, #62). All
three were accepted; #72 ranked first on fit. Pasted verbatim, ending with the
fenced JSON block:

````
## Summary

All three issues are seeded bugs on the same repo (`codepath/pathreview-ai301-fa26-s1`), so the repo-level checks are identical across all of them: the repo is active (last push 2026-09-16, not archived), there's no CONTRIBUTING.md or AI_POLICY.md (silence passes the AI-policy check), and a maintainer (`Aburke225`, COLLABORATOR) is responsive — replied to issue #52 within ~6 days. None has a release or meaningful star count, so `project-in-use` fails as a preferred check for all three (doesn't affect verdict).

For issue #68 and #62, an issue-timeline "referenced" event pointed to a commit in another student's personal fork (`yulijasso/ai301-coursework`, `RadRebelSam/ai301-coursework`) — not a PR against this repo, and not a claim comment. Combined with the Path Review house rule (classmate claims never block), none of the three trip `not-claimed` or `not-abandoned-ground`.

**Ranked read-out (all three accepted):**

1. **#72 — `verify_password` raises `UnknownHashError`** (best fit): single-file security fix in `core/security.py` with an explicit 1–2 hour estimate and an existing `xfail` test to un-mark. Directly hits the "safety layers" half of your stated growth area, and the acceptance bar is objective (test passes, marker removed), not a matter of taste.
2. **#68 — keyword search `ZeroDivisionError` on empty index**: single-file fix in `rag/retriever/keyword_search.py`, 2–4 hour estimate, same xfail-test pattern. Hits the "retrieval" half of your stated growth area.
3. **#62 — health check references nonexistent `settings.redis_host`**: touches `api/routes/health.py` and `core/config.py`, with a clear local repro (`GET /health` with Redis up). Good backend-API fit and a real request path end-to-end, but it's a thinner fix (one attribute swap) than #72/#68 and has no test file named, so ranks third.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/72",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived:false, pushedAt 2026-09-16T21:48:27Z (3 days before capture)"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees: [], comments: [], no PRs found searching '72 in:body,title'"},
      {"name": "not-abandoned-ground", "grade": "pass", "evidence": "0 closed-unmerged PRs reference #72"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "body names exactly core/security.py + tests/unit/test_security.py, estimate '1–2 hours'"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md and AI_POLICY.md both 404 (repo root and .github/) — silence passes"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Aburke225 (COLLABORATOR) first-replied on issue #52 ~6 days after it opened"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels: good first issue, tier-1; body names exact files to edit"},
      {"name": "project-in-use", "grade": "fail", "evidence": "no releases found; stargazerCount: 1"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived:false, pushedAt 2026-09-16T21:48:27Z"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees: [], comments: []; timeline 'referenced' event is a commit in yulijasso's personal fork, not a PR against this repo"},
      {"name": "not-abandoned-ground", "grade": "pass", "evidence": "0 closed-unmerged PRs reference #68"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "body names rag/retriever/keyword_search.py + tests/unit/test_keyword_search.py, estimate '2–4 hours'"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "no CONTRIBUTING.md/AI_POLICY.md found — silence passes"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Aburke225 (COLLABORATOR) first-replied on issue #52 ~6 days after it opened"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels: good first issue, tier-1; body names exact files to edit"},
      {"name": "project-in-use", "grade": "fail", "evidence": "no releases found; stargazerCount: 1"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "isArchived:false, pushedAt 2026-09-16T21:48:27Z"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees: [], comments: []; timeline 'referenced' event is a commit in RadRebelSam's personal fork, not a PR against this repo"},
      {"name": "not-abandoned-ground", "grade": "pass", "evidence": "0 closed-unmerged PRs reference #62"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "body names api/routes/health.py + core/config.py with explicit repro steps (GET /health)"},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "no CONTRIBUTING.md/AI_POLICY.md found — silence passes"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Aburke225 (COLLABORATOR) first-replied on issue #52 ~6 days after it opened"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "labels: good first issue, tier-1; body names exact files to edit"},
      {"name": "project-in-use", "grade": "fail", "evidence": "no releases found; stargazerCount: 1"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

Three runs, in order:

1. `2/4` — partial smoke run, `--only issue-01,issue-04,issue-12,issue-13`, to
   check the plumbing on one issue from each of four categories before paying for
   a full run. It disagreed on both accepts.
2. `7/7` — partial re-run, `--only issue-01,issue-04,issue-09,issue-11,issue-15,issue-19,issue-20`,
   after rewriting `scope-bounded` and splitting out `not-abandoned-ground`. This
   set is the two issues that had just failed plus five canaries the rewrite could
   plausibly have broken.
3. `20/20` — the full confirming run, written to `eval-run.txt` with `--save-run`.
   Its agreement line reads `agreement: 20/20 scored items  (bar: 18/20: PASS)`,
   and the category tallies read
   `claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`.

**Issue analysis**

`issue-01` (conda/conda#16475, "Add permanent docs for installing PyPI packages
with `conda install`").

- My rubric's decision on run 1: **reject**, on `scope-bounded`.
- Gold label: **accept** (category `clear-accept`).

My first version of `scope-bounded` failed an issue when its body "lists separate
work items" or "leaves a content, asset, or design decision open ('TBD',
'consider', ...)". Issue-01 trips both readings on the surface. Its "Proposed
changes" section enumerates five headed sub-tasks — add a new task page, update
`manage-pkgs.rst`, update `pip-interoperability.rst`, update `new-features.md`,
and "Consider a global `troubleshooting.rst` entry" — and that last one is
explicitly hedged as "Lower priority, but worth naming". My check counted the
five headings as five work items and the hedge as an unsettled deliverable, so it
rejected.

That reasoning was wrong about what the issue actually asks for. All five edits
are one coherent documentation change, landing in one pull request: the feature
being documented is already going GA, so nothing about *what* to write is under
debate, only the prose. Counting headings was a proxy for size, and it was a bad
proxy — `issue-14` (lq-ai#490) touches six files with line-level instructions and
is gold `accept` too. The real umbrella signal is not "several sub-headings" but
"links to other issue numbers, or says the work should be split up", which is what
`issue-10`'s megaissue and `issue-05`'s ongoing annotation drive actually do. I
narrowed trigger (a) to exactly that, deleted the "consider/TBD" trigger, and
added an explicit non-failure list naming "a change that touches several files or
several pages in one pass". Run 2 put issue-01 at `accept`.

**Check rationale**

From `tools/issue-select/rubric.md`, the `not-abandoned-ground` check, as it is
currently written:

> | `not-abandoned-ground` | The `linked PRs:` states on the `this issue:` line, plus any pull requests mentioned in the thread. | Fewer than two closed-unmerged pull requests have been written against this issue. Two or more people who got as far as a pull request and still did not land it is evidence the work is harder than it reads, whatever the label says. Merged pull requests are not abandoned attempts and do not count; neither does a claim comment that never produced a pull request. | required |

This check did not exist in my first rubric. It exists because of `issue-15`
(zulip#19589), which passes every other check I have: the repo is alive, it is
labelled `good first issue` and `help wanted`, it has no assignee, and zulipbot
has auto-unassigned every previous claimer, so `not-claimed` clears it honestly.
Gold rejects it. What is actually wrong with it is only visible as a count: open
since 2021, two closed-unmerged linked PRs (#20840, #23123), and roughly ten
people who claimed it and vanished. The evidence guide names this directly — "an
issue open for years with several abandoned attempts (closed, unmerged PRs in its
history) is telling you something about its real difficulty."

I wrote it as a count of closed-unmerged pull requests rather than as "has been
open a long time" or "lots of claim comments" because the threshold has to
separate issue-15 from `issue-09` (conda#7617), which is gold `accept` despite
being open since 2018 and having an abandoned 2022 claimer. Age separates them
badly — both are old. Claim comments separate them badly — issue-09 has one.
Closed-unmerged PRs separate them cleanly: issue-15 has two, issue-09 has one.
So the threshold is "fewer than two", and the two exclusions in the last sentence
exist so that a merged PR (normal progress) and a claim that never produced code
(cheap talk) do not inflate the count.

**Trade-offs**

It gives up issues that two people bounced off for reasons that have nothing to do
with difficulty. Hacktoberfest drive-bys, a contributor who changed jobs, a PR
closed because it was stale rather than wrong — all three read identically to my
check, which sees only a count of closed-unmerged PRs and concludes the work is
harder than it looks. A genuinely easy issue that happens to sit in a popular repo
during October will accumulate two abandoned PRs on its own, and my rubric will
never look at it again.

The canary I re-ran for this was `issue-09`, in run 2 (`--only ...issue-09...`). It
is the nearest miss to the threshold in the set — one closed-unmerged linked PR
(#11627) plus a 2022 claimer who never delivered — and it is gold `accept`. If I
had written the threshold as "one or more" instead of "two or more", or if I had
let claim comments count toward the total, issue-09 would have flipped to `reject`
and I would have lost a `clear-accept`. It came back `accept` in run 2 and again in
the full run, so the threshold is sitting one step above the nearest legitimate
case, which is as tight as I am willing to draw it.

---

## Selection rationale

**Selection rationale**

*1. Fit to my interests and to the time available.*

I picked #72 because it is a backend Python fix in the place I actually want more
practice: `core/security.py`, on the path where a stored credential gets checked.
The whole change is "make verification fail closed instead of raising", which is a
security-shaped judgement in a two-line surface — that is the kind of problem I'd
rather spend six hours on than a styling pass. The issue estimates 1–2 hours and
names both files, so even if I am slow I am well inside the time I have, and the
finish line is objective: the `xfail` marker comes off `tests/unit/test_security.py`
and the test passes. Nothing about "is this good enough" is a matter of taste.

*2. What the verdict identified correctly, and what I weighed that the rubric could
not.*

The rubric got the mechanical facts right and I trust them: nobody is assigned, no
open PR references it, the repo was pushed three days before I looked, there is no
contribution policy to violate, and the body names exact files, so
`newcomer-signposted` passes on evidence rather than vibes. It also correctly
refused to let `project-in-use` matter — the repo has one star and no releases,
which would be damning for a real project and means nothing for a classroom fork.

What it could not weigh is that all three candidates passed. The rubric ranked #72
first, but its ranking only knows my fit profile as text; it does not know how the
three actually felt when I read them. Authentication is a domain I can reason
about from first principles — a malformed hash should fail closed, and I can say
why — whereas #68 sits inside a retrieval pipeline I have never worked in, and I
think I would spend most of the budget understanding the scoring code before
writing a line of the fix.
I also preferred #72 over #62 for a reason the rubric explicitly cannot see: #62 is
a one-attribute swap, and I would rather have something with a real test to point
at in the PR than something I finish in ten minutes.

*3. The anticipated difficulty in claiming it.*

Low, and the house rule is the reason. Path Review is a classroom and other
students' claim comments do not block an issue, so even if a classmate comments
before I do, I claim anyway — credit attaches to the pull request I open, not to
whether it merges. #72 had no comments and no assignee when I graded it, so I am
not even in that situation yet. The real risk is not the claim, it is that a
classmate opens a PR first and I end up duplicating work; I'll check for open PRs
referencing #72 again right before I start Unit 2. I have not commented on the
issue yet — Unit 2 teaches the claim comment and the voice guide before I post it.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
