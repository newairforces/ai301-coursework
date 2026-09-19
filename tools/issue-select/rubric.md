# Rubric: is this a good first issue?

Five things sink a first contribution before any code is written: the repo
is dead, somebody else already owns the issue, better-equipped people have
already tried and walked away, the work is not one pull request's worth,
or the project's rules refuse an AI-assisted workflow. The five required
checks below are disqualifiers, ordered cheapest first. The three
preferred checks never reject anything; they rank what survives.

All recency thresholds are measured against the bundle's stated capture
date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-alive` | The repo line (`archived:`) and the "last 5 default-branch commits" list in the repo-facts block; in live mode, the repo front page's newest-commit date and archived banner. | The repo is not archived AND at least one default-branch commit is dated within 180 days of the capture date. A bot commit counts only when it merges a human pull request (e.g. "Merge pull request #N from user/branch"); a run of pure bot housekeeping with no human work in the window fails. | required |
| `not-claimed` | The `this issue:` line (`assignees:`, and `linked PRs:` with each PR's state) plus every comment in the thread, with its date and author_association. | No assignee, AND no linked or thread-mentioned pull request in the `open` state, AND no unretracted claim comment ("I'll take this", "working on this", "/assign", "@bot claim") dated within 90 days of the capture date, AND no maintainer statement reserving the work for a named person. A claim is retracted, and does not fail this check, when a maintainer or a bot has since unassigned or abandoned it, or when the pull requests it produced are all closed or merged without resolving the issue. | required |
| `not-abandoned-ground` | The `linked PRs:` states on the `this issue:` line, plus any pull requests mentioned in the thread. | Fewer than two closed-unmerged pull requests have been written against this issue. Two or more people who got as far as a pull request and still did not land it is evidence the work is harder than it reads, whatever the label says. Merged pull requests are not abandoned attempts and do not count; neither does a claim comment that never produced a pull request. | required |
| `scope-bounded` | The issue title and body, the labels line, and the thread. | The issue asks for one deliverable a newcomer could finish in a single pull request. It fails on any of: (a) an umbrella, tracking, or "mega" issue — its body lists other issue numbers, or it says outright that the work should be split into separate issues or pull requests; (b) an open-ended or ongoing effort with no single completion state ("PRs welcome both big and small", "incrementally adding more"); (c) an unendorsed new feature — the issue proposes a new feature or user-facing surface, no maintainer has applied a label or commented in agreement, and a required input is still unspecified ("asset TBD"); (d) a pure usage or support question; (e) a maintainer saying the fix needs changes to core internals. Otherwise it passes, and in particular these do NOT fail it: a terse or one-line body; a bare acceptance-criteria checklist; a bug report with no reproduction steps; a change that touches several files or several pages in one pass; a list of similar small items meant to be done together; a bug body that names more than one possible cause or offers optional follow-up suggestions; a documentation request, which is not a new feature for the purposes of (c). | required |
| `ai-policy-permits` | The "contribution policy" line in the repo-facts block; in live mode, `CONTRIBUTING.md`, `.github/CONTRIBUTING.md`, any `AI_POLICY.md` / `AI_USAGE_POLICY.md` they link, and PR-template disclosure checkboxes. | The stated policy does not refuse AI-assisted contributions. Silence passes: a repo with no CONTRIBUTING.md, or one that says nothing about AI, passes. Conditions pass: disclosure, personal understanding, testing, and human-review requirements are terms to follow, not bans. It fails only on a refusal — the policy says AI-generated or AI-assisted contributions are not accepted, or are closed, or discourages them to the point that suspected-AI pull requests get closed. | required |
| `maintainer-responsive` | The "maintainer first-response sample" list in the repo-facts block; in live mode, the first Owner/Member/Collaborator reply on a few recently updated issues. | At least one issue in the sample drew a first maintainer response within 30 days. Ranks accepted issues by how fast a newcomer can expect an answer; a thin or silent sample never rejects, because `repo-alive` already tested the commit history for whether anyone is home. | preferred |
| `newcomer-signposted` | The labels line, the issue body, and the thread. | The issue carries a `good first issue`, `help wanted`, `easy`, or equivalent maintainer-applied label, OR the body names the files, directories, or acceptance criteria to work from. Ranks accepted issues by how much guidance comes for free. | preferred |
| `project-in-use` | The `latest release` line and the star count on the repo line. | A published release dated within 365 days of the capture date, OR at least 100 stars. Ranks accepted issues by whether the merged work reaches real users; a young repo with no release yet is not disqualified, because `repo-alive` already tested whether anyone is home. | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. Any single
required `fail` produces `reject` — these checks are disqualifiers, not
points to be totalled, so a strong showing elsewhere never offsets one.

`unclear` counts as `fail` on a required check: evidence that is genuinely
absent is not evidence that the issue is safe to take. On a preferred
check, `unclear` is reported and drops that issue in the ranking.

Preferred checks never change a verdict. They order the issues that were
accepted: `maintainer-responsive` first, then `newcomer-signposted`, then
`project-in-use`.
