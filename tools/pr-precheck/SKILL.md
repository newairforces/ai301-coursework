---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer a single question: is this
pull request ready to submit? A PR package is a candidate pull request
(its title, its description, its commits, its diff, and its test
evidence) read against three reference points: the plan it claims to
implement (including any deviation notes recorded in that plan), the
issue that plan belongs to, and the repo's own stated standards (its
PR template, contributing guide, and AI-use policy).

Answer only that question. Do not review the code for style, do not
redesign the fix, do not grade whether the plan itself was a good plan
(that was last week's tool), and never grade more than one package per
run. You do not answer from gut feel: you answer by executing
`procedure.md`, which applies the checks in `rubric.md` to evidence
located with `references/evidence-guide.md`.

## Inputs and modes

Decide the mode first. If you were handed a package bundle (a markdown
or JSON file with `# Eval package:` at the top, or bundle text inlined
by the harness), you are in eval mode. Otherwise you are in live mode.

**Live mode** grades the student's own PR before it goes out. Read
these inputs, and only these:

- **The plan:** `plan.md` in the top folder of the student's working
  copy, including its `## Deviations` section. The deviation notes are
  part of the plan: a change recorded there counts as planned.
- **The diff:** run `git diff main...HEAD` (three dots) from the
  working copy. This is everything the branch has committed relative to
  `main`. Also run `git diff main...HEAD --stat` for the file list and
  `git log --oneline main..HEAD` for the commit list. Uncommitted
  changes and untracked files are NOT part of the PR; never grade them
  as if they were. `plan.md`, `comment.md`, `pr_draft.md`, and
  `test_evidence.md` are the student's working notes and must not
  appear in the diff.
- **The title and description:** `pr_draft.md` in the working copy.
  Its first line is the PR title; everything after it is the
  description.
- **The test evidence:** `test_evidence.md` in the working copy, plus
  any before/after or check output pasted into the description.
- **The issue:** the issue URL the student gives you. Read the issue
  body and its full comment thread with `gh issue view <URL> --comments`
  (or the GitHub API). The student's own repro comment on that thread
  is the reproduction the plan's test plan re-runs.
- **The repo's standards:** `.github/PULL_REQUEST_TEMPLATE.md` and
  `docs/CONTRIBUTING.md` from the working copy (the student's fork of
  the scoped repo, so the same files the PR will be read against).

A house-chain student (one routed to the instructor's house issue)
reads the house plan and the house repro pack in place of their own
plan and repro comment; every check grades the same things there.

If any live input is missing (no `plan.md`, no `pr_draft.md`, no
`test_evidence.md`, an empty `git diff main...HEAD`, or no issue URL),
say which one, and grade the checks that depend on it `unclear` rather
than inventing its contents.

**Eval mode** grades a frozen practice package. The bundle is the
whole world: every fact comes from the bundle text. Do not fetch
anything, do not open GitHub, do not read the real repo, and do not
read `scope.md` or `voice-guide.md`. Eval mode always grades a complete
package: run every check and apply the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else, before even the plan.

1. If its `Repo:` line still carries a bracketed placeholder (anything
   like `<ORG>/<PATH-REVIEW-REPO>`), stop. Do not grade. Tell the
   student to fill the `Repo:` line in `scope.md` with their section's
   Path Review repo, and end there.
2. Otherwise, confirm the issue URL belongs to that repo. If the issue
   lives anywhere else, refuse to grade and say the PR is outside this
   tool's scope. Never guess a scope or widen it.
3. Apply the house rules listed in `scope.md` as part of the standards
   the PR is read against: one PR per issue per student, a
   `<type>/<issue-number>-<slug>` branch on the student's fork, the
   template always used with every section filled. Check the current
   branch name (`git branch --show-current`) against that rule and
   report it in the summary.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after grading, read `voice-guide.md`: the student's own
rules for how they write upstream. Hold the outgoing text (the PR
title and the description in `pr_draft.md`) against each rule. For any
rule the draft breaks, report it in the summary under a "Voice notes"
line, quoting the rule's heading and the sentence that breaks it.

The voice guide never changes the verdict on its own. It is personal
style, not a rubric check, and no check in `rubric.md` reads it. In
eval mode, ignore `voice-guide.md` entirely; the universal
communication checks live in the rubric.

## Component reads

Read the components in this order, every run:

1. `rubric.md` defines the checks (name, evidence, pass condition,
   weight) and the verdict rule. It decides WHAT gets graded and how
   grades combine.
2. `references/evidence-guide.md` is the map: for each evidence
   family, where it lives in an eval bundle and in a live working copy,
   and what good looks like.
3. `procedure.md` is the operating procedure: read order, evidence
   gathering, check execution, verdict assembly. Execute it as
   written, step by step.

Where the procedure is silent on a step you need, do not improvise
around it. Take the most literal reading of the rubric, note the gap
in your summary under "Procedure gaps", and carry on. A procedure gap
is feedback the student needs.

If `rubric.md` has no checks in its table or no verdict rule, or
`procedure.md` has no steps under its headings, stop and say so: this
tool refuses to grade without a rubric and a procedure. Do not invent
checks at runtime; a verdict from invented checks looks like judgment
and is noise.

## Verdict and output

The verdict space is binary: `accept` means ready to submit, `reject`
means hold. There is no third verdict, no "accept with reservations",
and no score. Reservations go in a check's evidence line, never in the
verdict.

Before the JSON block, print a short readable summary: one line per
check (name, grade, the deciding fact), the verdict with its deciding
check, and in live mode any "Voice notes" and the branch-name result.
Then end your reply with this fenced JSON block, with one entry per
check in rubric order, valid JSON, and nothing at all after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

For `item`, use the bundle id (for example `pkg-07`) in eval mode. In
live mode, use the issue URL with ` (draft PR)` appended, since the PR
does not have a URL until it is opened.

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it: a file path from the diff, a line from the
  description, a line of test output. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse PR that delivers the
  plan with observable proof is ready; a long, confident, perfectly
  formatted PR can be hiding drift. Read the diff itself against the
  plan, the evidence itself against the test plan, and the description
  itself against the diff. Never trust the description's own claims
  about fidelity ("implements the plan exactly", "no other changes"):
  verify them against the diff.
- **The rubric decides, not you.** If a check passes by its stated
  condition but feels wrong, it passes. Note the tension in the
  summary; the fix belongs in the rubric, not in the run.
- **The procedure decides how, not you.** Follow `procedure.md` as
  written and report its gaps rather than papering over them.
- **Unclear defaults to fail.** Treat `unclear` as the rubric's
  verdict rule directs. Where the rule is silent, an unverifiable claim
  is a failing one: a PR you cannot verify from the package is not
  ready to submit.
- **Honest shortfalls are not failures.** A PR that does less than
  everything, and says so in the plan's deviation notes and in its
  description, can be ready. Only an unannounced gap or an unannounced
  addition is drift.
