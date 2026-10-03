# Evidence guide: where evidence lives in a plan package

The map the rubric's checks read from, and the procedure's gathering
stage walks. For each family: where the fact is, and what good looks
like when you find it.

## Diagnosis and grounding

**Where it lives.** The plan's stated cause sits in its opening
paragraph or under a `Diagnosis`, `Problem statement`, or `Background`
heading. The reference it must answer to is the `## Repro evidence`
block, and inside that block the **control runs** carry most of the
weight: the lines beginning "Control", "Control run", or "Second
control". In live mode the plan is the student's draft `plan.md` and
the reference is their posted repro comment on the issue thread.

**What good looks like.** The stated cause explains every artifact in
the repro evidence, and no control contradicts it. The strongest form
quotes the control back: *"Both controls in the repro fit: no
background, no subtraction; width 2, no overshoot."* The failure this
family exists to catch is subtler than a wrong guess — it is a cause
that is plausible on its own and already excluded by evidence the
package contains. Three shapes recur. A control proves the component
the plan blames is working (the plan blames the collect operator while
the control shows collect producing `[null]` correctly at top level).
The evidence pins the failure at one stage and the plan names another
(the evidence shows values already lost in the parsed table, before any
cast could run, and the plan blames the cast). Or the plan waves away a
signal the evidence establishes, calling the version difference "a red
herring" while offering no evidence of its own. A plan that reaches
past its evidence and *says* it is reaching — "I have not yet verified
which layer clamps the viewport" — is doing something different, and
belongs under Honesty, not here.

## Scope

**Where it lives.** The plan's `Scope`, `In scope` / `Not in scope`
lines, and its `Proposed changes` or `Change` list. Read the two
against each other: the list is what the plan will actually do, and the
scope line is what it claims it will do.

**What good looks like.** Every proposed item is needed to fix the
reported failure, and anything adjacent is named and deferred with a
reason: *"Not in scope: the tombstone rework discussed in the thread;
it is the better long-term shape but a larger change, and I am
deliberately deferring it."* Scope creep is rarely disguised — it
announces itself in a sentence arguing that the honest small fix is
beneath the problem: "rather than patch just the save path", "rather
than spot-fix the one emission site", "it fixes the class rather than
the instance". Downstream of that sentence you will find the actual
tells: a restructure into modules, a new abstraction unifying other
code paths, a dependency or runtime version bump, a new user-facing
option or settings field, a CI matrix, or an adjacent bug being fixed
in passing. Any one of those appearing in the change list, unasked for
by the issue, is the failure. Note that a plan touching several sites
of the *same* defect is not creep: clamping two subtractions in one
function is one change.

## Executability

**Where it lives.** The plan's `Files`, `Files and areas`, `Approach`,
or `Steps` sections — whatever names what will be changed and in what
order.

**What good looks like.** A stranger could open the named file and make
the first change without asking a question. Good names a path and a
move: *"in the command construction in `crates/cli/src/decompress.rs`,
append `--` before the file path for every spawned decompression
tool."* The failure is a plan whose steps are all still investigation —
"profile starship on Windows to find the slow parts", "investigate how
lazygit reads mouse events (gocui? tcell? not sure which layer)", "fix
the scrolling once the cause is clear". Those are a research agenda,
and a reader cannot start them. A near relative worth catching: the
plan whose only concrete step is wrapping the unlocated failure in a
safety net ("add a recover() somewhere around linter execution") while
the actual fix stays vague. Precision has a floor, not a ceiling: a
plan that names the area and the change while leaving the exact
function to be pinned during implementation — *"exact functions to be
pinned in the PR after tracing the query issuance with debug logs,
which I have working"* — is executable, because the first move is
clear and the method for the rest is stated.

## Test plan

**Where it lives.** The plan's `Test plan` section, read beside the
repro evidence's numbered steps and its artifacts.

**What good looks like.** It re-runs the reproduction and names what
will be different, in terms someone else could check: an exit code, a
specific printed line, a named test passing, a control staying
unchanged. *"Re-run the repro command, expect drawn output and exit 0;
re-run both controls unchanged; add the repro as an integration test
pinned at width 1."* The best ones also say the controls must *not*
change, which is how a reader knows the fix was targeted. The failure
is success stated as a direction or a feeling: "the prompt should feel
fast in big repos", "`starship timings` should look much better",
"scrolling should work correctly afterwards, and nothing else should
feel broken". Those cannot be checked by anyone but the author, and
often not even by them. Length is not the measure — a single sentence
naming an exit code is decisive, and a paragraph of adjectives is not.

## Honesty

**Where it lives.** The plan's `Risk`, `Risks and unknowns`, or open-
question lines, and any `## Deviations` section (empty before the build,
filled after). Read them against the confidence the rest of the plan
projects.

**What good looks like.** The plan names something it has not settled
and says what it will do about it: *"I have not yet measured the
per-print cost of the generation comparison; if it shows up in the
print benchmark I will move the check to the two growth-adjacent call
sites only, and I flag that trade-off for review."* Naming an unknown
is not weakness here, and it never fails a check on its own — it is
what separates reaching past the evidence honestly from contradicting
it. After a build, this is also where a deviation from the posted plan
gets recorded, in the plan itself rather than only in the diff.

## Comms

**Where it lives.** Two places meet here. The `## Thread highlights`
list carries who said what, with each commenter's association in
parentheses — OWNER, MEMBER, COLLABORATOR mark a maintainer; NONE and
CONTRIBUTOR usually do not. The repo-facts block's `contribution
policy:` line carries the repo's asks, including any AI policy. The
candidate plan comment is what gets read against both. In live mode:
the live issue thread, `CONTRIBUTING.md` and any `AI_POLICY.md` it
links, and the draft comment.

**What good looks like, against the thread.** Where a maintainer has
settled something — named the responsible file, chosen between fix
options, confirmed the diagnosis — the plan either follows it or says
why not. *"I would like to implement option 2 from the discussion
here… I have read PR #2089, which takes the same route; if that lands
first I will rebase my tests onto it rather than duplicate the
change."* The failure is a plan that proposes something else entirely
while the maintainer's direction sits unmentioned in the thread above
it — proposing a documentation-only workaround when the owner has
already located the culprit in a source file and posted a patched
binary. Analysis from a non-maintainer commenter is context worth
citing, not a direction that binds the plan.

**What good looks like, against the policy.** Read the scope of the ask,
not the presence of the word AI. Assume the author is working with AI
assistance — every package here is. So a policy requiring disclosure of
AI use in issues or comments applies, and a comment carrying no
disclosure fails, however good the plan beneath it. A policy asking for
disclosure only in pull requests, or asking that comments be written by
a human in their own words, is satisfied without a disclosure line. Most
repos state nothing, and silence is not a restriction.
