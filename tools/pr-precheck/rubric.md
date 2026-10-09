# Rubric: is this pull request ready to submit?

A pull request is ready when the diff delivers the plan it claims to
deliver and nothing it does not admit to, the description says what
the diff actually does, the evidence shows the bug's own reproduction
flipping and the repo's own checks run, a reviewer can see the fix
without digging through leftovers, and the repo's stated asks (its
template, its required artifacts, its AI-use policy) are met.

Six required checks, one or two per failure family, each a comparison
between two concrete things in the package. None of them reads the
write-up's shape: a terse PR that meets all six is ready, and a long
polished one can fail the first.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diff-matches-plan` | Every file and hunk in the diff, read against the plan's scope, files list, approach, and not-in-scope line, plus any deviation note recorded in the plan or restated in the description. | Every hunk traces to something the plan or a deviation note names, or to an artifact the repo's stated asks require (a test for the change, a CHANGELOG or whatsnew entry, i18n strings, regenerated docs or cheatsheets), or to a comment or docstring on the function being fixed. AND every deliverable the plan names is in the diff, unless a deviation note or the description says it was left out. Fails when the diff adds unplanned behavior: a new flag, setting, or config option; a rewrite or refactor of code the fix does not need; a renaming pass; edits to a file the plan never names that change behavior. Fails equally when a named deliverable is missing with no note. Something the plan's not-in-scope line explicitly excludes, appearing in the diff, always fails. An honest, disclosed shortfall passes. | required |
| `description-matches-diff` | Each claim the description makes about what the change does or what it touches (including "implements the plan exactly", "no other changes", "also documents X"), read against the diff. | Every change the description claims is visible in the diff, and every behavior-changing hunk in the diff is mentioned in the description. Fails when the description claims something the diff does not contain (a docs update with no docs file in the diff) or claims fidelity ("exactly as planned", "no functional changes outside X") that the diff contradicts. A description that is short but accurate passes. | required |
| `repro-rerun-decisive` | The test-evidence section (and any before/after in the description), read against the plan's test plan and the repro evidence's steps and inputs. | The evidence re-runs the plan's reproduction on the path that actually failed (the same input, configuration, or condition that triggered the bug), shows real output, and names an after-result that matches the plan's expected-after. The before is shown or is the repro evidence's own recorded failure. If the plan's test plan names more than one failure mode of the bug (two repro commands that each crashed or misbehaved before the fix), each one is re-run with output. A test-plan case that checks new behavior the fix itself adds (a new warning, a new error message, a control staying unchanged) is met by a stated result or a named test that covers it; it does not need pasted output. Fails when the evidence is a feeling or a summary ("tested locally, works now", "verified on my machine for a day", "colors work now"), when it runs only a control or a different path that was never broken (single-file instead of the two-file repro, a GET instead of the failing POST), or when a failing case the test plan named is never re-run. A control run alongside the real repro is good; a control instead of it fails. | required |
| `repo-checks-run` | The test evidence and description, read against the checks the repo's stated asks name (PR template checklist, contributing guide's test command, e.g. `cargo test`, `yarn test`, `make test-unit`) and the suite the plan's test plan names. | When the repo or the plan names a test suite or check, the evidence names the command and states its outcome (a count, "passes", or the failing output). A failing or not-applicable check that is shown as it is and explained passes: honest results count. Fails when a named suite is never mentioned, or when the outcome is only "all tests pass" / "tests added; all pass" with no command named. If neither the repo nor the plan names any suite or check, passes with that absence as the evidence. | required |
| `diff-clean` | The unified diff's added and removed lines, and the commit list. | The diff contains only the change and what supports it. Fails on any of: a debug print or log line left in (`eprintln!("DEBUG`, `print(`, `console.log`, `dbg!`); commented-out code (an old attempt or a disabled line); a dead function or `allow(dead_code)`/`_unused` helper; a new TODO or FIXME; formatting, re-indent, or import-reorder churn on lines the fix does not otherwise touch; a standalone block of lines deleted and re-added identically. Unchanged lines that reappear as `-`/`+` pairs inside a multi-line statement whose other line the fix does change (an argument list where one argument changed) are a diff rendering artifact, not churn. Commit messages like "wip", "fix", "misc cleanups while debugging" are supporting evidence, not a failure on their own. A comment or docstring that explains the fix is not debris. | required |
| `standards-met` | The repo-facts block's (live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`) stated PR asks and AI-use policy, read against the description and the diff. | Every section, checklist item, link, or artifact the repo states it requires is present with real content, or explicitly marked not applicable with a reason: e.g. a `closes #NNN` line when the template asks for one, a required checklist, a whatsnew or CHANGELOG entry in the diff when the guide asks for one, named template sections filled. AND when the repo's stated policy requires disclosing AI use in pull requests (or "all AI usage in any form"), the description contains a disclosure stating the tool and the extent. Every package is AI-assisted work, so a disclosure requirement always applies and is never waived for lack of proof. A policy that only asks that comments be human-written in own words is met by an own-words statement or by no AI language at all. Live mode: the course requires an AI-use disclosure in the Notes for Reviewers section of every PR, so it is always a stated ask there. If the repo states no template, no required artifacts, and no AI policy, passes with that absence as the evidence. | required |
| `thread-engaged` | The description, read against the thread highlights: any maintainer question, settled direction, or caveat. | The description names and engages each maintainer question or caveat (follows it, or says why not). Ranks a PR that answers the room above one that ignores it. | preferred |
| `commits-focused` | The commit list. | Each commit message says what its commit changes and follows the repo's stated commit convention if it has one. Ranks a history a reviewer can read commit by commit. | preferred |

## Verdict rule

Accept (ready to submit) if and only if all six `required` checks grade
`pass`. Any single required `fail` produces `reject`. These are
disqualifiers, not points: decisive evidence never buys back a silent
extra feature, and a clean diff never buys back an unmet AI-disclosure
policy.

`unclear` counts as `fail` on a required check: a claim I cannot verify
from the package is a claim a reviewer cannot verify either. The only
exception is already written into the pass conditions: when the thing a
check reads for does not exist in the package (no named suite for
`repo-checks-run`, no stated template, artifact, or AI policy for
`standards-met`), the check passes, with the absence as its evidence
line. Absent asks are not failures; ignored asks are.

Preferred checks never change the verdict. They rank PRs that already
passed: `thread-engaged` first, then `commits-focused`.

<!--
Coverage of the four reject families:
- silent drift  -> diff-matches-plan, description-matches-diff
- not tested    -> repro-rerun-decisive, repo-checks-run
- unreviewable  -> diff-clean
- standards wall -> standards-met
-->
