# Evidence guide: where evidence lives in a PR package

Each family below names where its evidence lives in an eval bundle
(the `pkg-NN.md` practice packages), where it lives in my own PR in
live mode, and what good looks like.

Live-mode file map, all in the top folder of my fork's working copy
on my fix branch:

- `plan.md`: my week-3 plan, including `## Deviations`
- `git diff main...HEAD` (three dots): the PR's diff; `--stat` for the
  file list; `git log --oneline main..HEAD` for the commits
- `pr_draft.md`: first line is the PR title, the rest is the
  description in the repo's template
- `test_evidence.md`: before/after repro runs and the four checks
  (`make test-unit`, `make test-integration`, `make lint`,
  `make typecheck`)
- `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`: the
  repo's stated asks
- the issue thread on GitHub (`gh issue view <URL> --comments`): the
  issue, my repro comment, my plan comment, any maintainer direction

## Plan fidelity (harness category: silent-drift)

**Where it lives.** Eval: the "Plan context" block holds the plan's
scope, its `Files:` list, its numbered deliverables, its "Not in
scope:" line, and any deviation or deferral note; the "Diff" section
under "Candidate PR" shows what actually changed (read the `+++ b/`
file headers for the file list); the "Description" holds the PR's own
fidelity claims. Live: `## Scope`, `## Files`, `## Approach`, and
`## Deviations` in `plan.md`; `git diff main...HEAD --stat` and the
full diff; the Summary and Changes sections of `pr_draft.md`.

**What good looks like.** Every changed file and hunk traces to the
plan's files and approach, a deviation note, or an artifact the repo
requires (tests, changelog). Every deliverable the plan names is in
the diff or is named as left out in both the plan and the description.
Every claim in the description ("adds a test", "documents the
limitation") points at a hunk that exists.

**Silent drift, both directions.** More than the plan: a new config
option, flag, or setting; a rewrite of a helper "while I was there"; a
renaming pass; edits to a file the plan never names (calib-03's second
hunk in `print/property.js`). Less than the plan: a named deliverable
(a docs page, a second fix) absent from the diff while the description
says it is there (pkg-17). The tell is a fidelity claim ("exactly as
planned", "no changes beyond the plan") sitting over a diff that
contradicts it. An honest deviation re-ties a mismatch only if it is
written down: in the plan's deviation notes and restated in the
description (pkg-13, pkg-16).

## Test evidence (harness category: not-tested)

**Where it lives.** Eval: the "Test evidence" section under "Candidate
PR", read against the plan's "Test plan:" sentence and the "Repro
evidence" paragraph at the top of the Plan context block; the repo
facts line naming the test command. Live: `test_evidence.md` and the
Testing section of `pr_draft.md`, read against `## Test plan` in
`plan.md` and the steps in my repro comment on the issue; the four
`make` targets the PR template lists.

**What good looks like.** The plan's own reproduction, with the same
input that triggered the bug, re-run on the branch with the real output
pasted: a before (or the repro's recorded failure) and an after that
matches the plan's expected-after, such as an exit code, a printed
value, a test going from `xfailed` to `passed`. Each failing case the
test plan names gets its own run. A control run alongside it is a
bonus. The repo's checks appear by command with their outcome
(`cargo test -p ignore passes (214 tests)`; `make test-integration`
"no tests ran", pasted as is, with why).

**What not-tested looks like.** "Tested locally, works now"; "verified
on my machine for a full day"; "all tests pass" with no command; only
the control path run (pkg-07 runs the single-file case, which never
broke); a different path run (pkg-14 runs a GET, the bug is a POST);
one of two named failing cases skipped (calib-04).

## Diff quality (harness category: unreviewable)

**Where it lives.** Eval: every `+` and `-` line in the "Diff" section,
and the "Commits" list. Live: `git diff main...HEAD` and
`git log --oneline main..HEAD`.

**What good looks like.** Every added and removed line is the fix, its
test, or a comment that explains the fix. Nothing on a line the fix
does not otherwise need to touch changes.

**Debris tells.** Debug output left in (`eprintln!("DEBUG ...")`,
`print(...)`, `dbg!`); commented-out code, including an earlier
attempt; a dead helper (`_unused_buffer_probe`, `count_open_fds` under
`allow(dead_code)`); a new TODO or FIXME; re-indent or whitespace churn;
an import-restructure hunk the fix does not need; a block of lines
deleted and re-added identically. Commit messages like "wip", "fix",
"fmt + cleanup", or "misc cleanups while debugging" usually travel with
these. In live mode, `plan.md`, `comment.md`, `pr_draft.md`, or
`test_evidence.md` showing up in `--stat` is debris too.

## Standards and comms (harness category: standards-wall)

**Where it lives.** Eval: the "Repo facts" block's `pull requests:`
line (template sections, checklist items, required links and
artifacts) and its `contribution policy:` line (AI-use policy); the
PR's "Description" and "Diff" show whether each ask is met. Live:
`.github/PULL_REQUEST_TEMPLATE.md` (Summary, Issue with `Closes #`,
Changes, Testing checklist, Screenshots / Demo, Notes for Reviewers)
and `docs/CONTRIBUTING.md` (branch naming, conventional commits,
xfail-marker removal, "fill out the PR template completely"); the
course's AI-use disclosure goes in Notes for Reviewers.

**What good looks like.** Every stated ask can be ticked off against
something concrete: each template section has real content or an
explicit "N/A" with why; a `closes #NNN` line when one is asked for; a
required changelog or whatsnew entry visible in the diff; the
checklist's boxes ticked only where true. When the policy requires AI
disclosure, the description says which tool and what it did (pkg-11:
"AI-assisted with me reviewing and understanding every line, and this
text is in my own words").

**What a standards wall looks like.** A great fix with the template's
asks visibly skipped: pandas' `closes #xxxx`, checklist, and whatsnew
entry all absent (pkg-01); a repo whose policy says "all AI usage in
any form must be disclosed" with no disclosure in the description
(pkg-20). A repo with no template and no policy has no wall to hit.
Whether the description's claims match the diff belongs to plan
fidelity, above, not here.
