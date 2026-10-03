# Rubric: is this plan ready to post and build from?

A plan is ready when the cause it names is the one the reproduction
actually pins down, the change it proposes is the one this issue needs
and no more, a stranger could start executing it, success is something
you can watch happen, and it does not walk past what the thread or the
repo already settled.

Five required checks, each a comparison between two concrete things in
the package. None of them reads the write-up's shape: a terse plan with
all five can be ready, and a long sectioned one can fail on the first.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `cause-grounded` | The plan's stated cause, read against what the repro-evidence block actually shows — its steps, its artifacts, and especially its control runs. | The stated cause explains the behavior the repro evidence shows, and nothing in that evidence rules it out. Fails when a control run contradicts the named cause (the plan blames a component the control proves is working), when the evidence pins the failure at a different stage than the cause names, or when the plan calls a signal the evidence establishes "a red herring" without new evidence of its own. A cause that merely goes further than the evidence proves, and says so, does not fail here — that is an unknown, not a contradiction. | required |
| `scope-bounded` | The plan's in-scope and not-in-scope statements, read against its own list of proposed changes. | The proposed work is the change this issue needs. Fails when the plan also proposes work the issue did not ask for: a refactor or restructure of the surrounding code, a rewrite of the existing mechanism "properly", unifying other code paths through one new abstraction, a dependency or runtime version bump, a new user-facing option or setting, new CI infrastructure, or fixing an adjacent bug found along the way. The tell is a plan that argues it is "fixing the class rather than the instance" — that argument is how scope creep announces itself. Naming such work and explicitly deferring it is the opposite of this failure and passes. | required |
| `executable` | The plan's files, areas, and approach sections: what will be changed and what the first concrete move is. | A stranger could start work from this plan without asking the author anything. It names the files or code areas to change and a concrete change to make there. Fails when the plan's steps are still investigation — "profile it", "investigate how X works", "try different approaches", "look into what changed", "fix it once the cause is clear" — or when its only concrete step is to add a safety net around a cause it has not located. Naming an area plus the concrete change, while leaving the exact function to be pinned during implementation, passes: that is normal precision, not a missing plan. | required |
| `test-plan-decisive` | The plan's test plan, read against the repro evidence's steps, artifacts, and control runs. | Success is something a reader could watch happen. The test plan re-runs the reproduction (or the equivalent check) and names the observable outcome that will have changed — an exit code, a printed value, a specific output line, a passing named test. Fails when success is a feeling or a direction rather than an observation: "should feel fast", "timings should look much better", "scrolling should work and nothing else should feel broken", "the bug should be gone". | required |
| `thread-aligned` | The plan comment and plan, read against the thread highlights (who said what, and whether a maintainer settled a direction) and the repo-facts block's contribution policy. | The plan does not walk past what the room already decided. Fails when a maintainer has settled a direction in the thread — identified the responsible code, chosen between options, or stated what the fix should be — and the plan neither follows it nor gives a reason for departing from it. Also fails when the repo's contribution policy requires disclosing AI assistance in comments and the plan comment carries no disclosure; every package here is produced with AI assistance, so that requirement always applies to the author and is never waived for lack of proof that AI was used. A policy that only asks for disclosure in pull requests, or that asks comments be written by a human in their own words, is satisfied without a disclosure line. Acknowledging prior art and open PRs, and saying how this plan relates to them, is what passing looks like. | required |
| `deferrals-named` | The plan's not-in-scope statement and any explicit deferrals. | The plan names at least one specific thing it is choosing not to do, with a reason. Ranks a plan that has drawn its own boundary above one that merely stayed small by accident. | preferred |
| `unknowns-stated` | The plan's risk, unknown, or open-question statements, read against its own confidence elsewhere. | The plan names at least one thing it has not yet verified and says what it will do about it, rather than presenting every step as settled. Ranks a plan whose author knows where their uncertainty is. | preferred |

## Verdict rule

Accept — ready to post and build from — if and only if every `required`
check grades `pass`. Any single required `fail` produces `reject`. These
are disqualifiers, not points to total: a decisive test plan never offsets
a cause the evidence contradicts.

`unclear` counts as `fail` on a required check. A plan I cannot verify
from the package is a plan I should not build from. The one exception is
a check whose evidence the package genuinely does not contain at all (no
thread highlights, no stated policy): absent context is not a failure, so
`thread-aligned` passes when the package carries no settled direction and
no policy requirement to meet.

Preferred checks never change a verdict. They rank plans that already
passed: `deferrals-named` first, then `unknowns-stated`.
