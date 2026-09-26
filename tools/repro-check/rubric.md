# Rubric: is this reproduction package ready to post?

A reproduction is proof only if a stranger can re-run it and land where I
landed. Six required checks, each one a comparison between two concrete
things rather than a judgement about how the writing looks. A long
confident report with nothing under it fails here; a terse honest one
passes.

Every check reads the candidate package against the issue it belongs to.
Recency and version thresholds are measured against the package's capture
date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `env-recorded` | The repro report's environment statement, read against the version and platform the issue names. | The report states the tool's version and the operating system or platform it ran on. If either differs from what the issue targets, the report names the difference rather than leaving the reader to spot it. Fails when no version is given, or when the platform is left out on an issue whose behavior is platform-specific. Hardware boasts ("RTX 4080, 64 GB") are not a version. | required |
| `steps-rerunnable` | The report's steps section, read as a stranger starting from a clean machine. | Every step is a literal command, input file, or configuration the reader could obtain and run. An input the report shows inline, or points at in the issue, counts as obtainable. Fails when any step depends on something the reader cannot get — a private repository, an unshareable config, an internal build — or when a step is described in prose with no command behind it. | required |
| `behavior-matches` | The artifacts the report pastes (output excerpts, logs, console text, exit codes), read against the specific failure the issue describes. | The artifact shows the same failure the issue reports: the same error or panic, the same exit status when both name one, or the same wrong output. A different error, a graceful message where the issue reports a crash, or a crash where the issue reports wrong output, all fail — those are adjacent bugs, not this one. An artifact that only shows setup succeeding is not the behavior. A report that states plainly it could not reproduce, and shows what it got instead, passes this check: an evidenced cannot-reproduce is a real result. | required |
| `claims-backed` | Every outcome asserted in the claim comment and the report, matched against the artifacts in the same package. | The report's stated conclusion is supported by an artifact it actually shows, and its confidence matches what that artifact shows. Fails when the conclusion rests on a run the report does not show, when it announces a diagnosis or root cause with no artifact demonstrating it, or when it calls the reproduction confirmed while its own artifact shows something else. Saying that the outcome already shown recurred — across repeated runs, a second machine, another version — is corroboration of a visible result, not a separate claim, and does not fail this check on its own; it fails only when that unshown run is what the conclusion rests on. | required |
| `repo-conventions` | The repo-facts block: the bug-report template's stated asks, and the contribution policy including any AI-use policy, each read against the two comments. | The two comments give what this repo asks of them, on both surfaces. **Template:** if the template requires confirming the bug on a particular version — the latest release, the main branch — the report tests that version or says why it could not. **AI disclosure:** every package in this course is produced with AI assistance, so a disclosure requirement always applies to the author and is never waived for lack of proof that AI was used. If the policy requires disclosing AI assistance in issues or comments, the comments must carry a disclosure naming the tool and the extent of its help; comments with no disclosure line at all fail, however strong the reproduction beneath them. Two things are not disclosure requirements and pass without one: a policy that only asks disclosure in pull requests (or states no ask for issue comments), and a policy that requires comments be written by a human in their own words, which is satisfied by comments that read as the author's own. Silence passes — most repos state no AI policy. | required |
| `claim-is-bounded` | The claim comment on its own. | The claim names this specific issue's content and says what the author will investigate next. Fails when it promises a fix, a deadline, or a date; when it demands assignment or asks for the issue to be reserved; or when it carries no substance at all (a bare "+1", "same here", or a request for status with nothing offered). Promising an investigation is right; promising an outcome is not. | required |
| `control-run` | The report's artifacts, looked at for a second contrasting run. | The report includes a run that isolates the trigger — the same command without the flag, the working input beside the broken one, the other language setting — so the difference and not just the failure is visible. Ranks a report that proves the boundary above one that only proves the failure. | preferred |
| `next-step-located` | The claim comment's statement of what comes next. | The next step names a concrete file, function, or code path rather than an intention to "look into it". Ranks claims that show the author already opened the code. | preferred |

## Verdict rule

Accept — ready to post — if and only if every `required` check grades
`pass`. Any single required `fail` produces `reject`. These checks are
disqualifiers, not points to total: a strong environment record never
offsets an artifact that shows the wrong failure.

`unclear` counts as `fail` on a required check. Proof I cannot verify is
proof that is not ready to go upstream. The one exception is the
claim-only draft state described in SKILL.md: checks reported `unclear`
with evidence `not yet applicable: claim-only draft` are left out of the
verdict entirely rather than counted as failures, so a claim-only draft is
judged on `repo-conventions` and `claim-is-bounded` alone.

Preferred checks never change a verdict. They rank packages that already
passed: `control-run` first, then `next-step-located`.
