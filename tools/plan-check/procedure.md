# Procedure: how this skill grades a plan package

Operating steps. Follow them in order, exactly as written. Where a step
cannot be completed because the package lacks the evidence it names,
record that and carry it into the check rather than guessing.

## Read order

Read the whole package before grading anything. The order matters,
because three of the five checks are comparisons, and a comparison is
only honest if you have read the reference side before the candidate
side.

1. **Read the issue context first.** Note, in one line each: the failure
   the issue reports, and any cause the issue itself proposes. The issue's
   proposed cause is a claim, not a fact — do not treat it as the
   reference for `cause-grounded`.
2. **Read the repro-evidence block second, before any part of the plan.**
   This is the reference side for `cause-grounded` and
   `test-plan-decisive`. Note: the steps run, the artifact each produced,
   and — separately and explicitly — **every control run and what it
   rules out**. Write the controls down before reading the plan. A
   control is the single most decisive piece of evidence in most of these
   packages, and it is easy to rationalize away once the plan's story is
   in your head.
3. **Read the thread highlights third.** Note whether any commenter
   carries a maintainer marker (OWNER, MEMBER, COLLABORATOR, CONTRIBUTOR
   on their own repo) and whether any of them settled a direction:
   identified the responsible code, chose between options, or said what
   the fix should be. Note non-maintainer analysis separately; it is
   context, not a direction.
4. **Read the repo-facts block fourth.** Note the contribution policy,
   and specifically whether it requires disclosing AI assistance in
   issue comments.
5. **Read the candidate plan fifth**, then **the candidate plan comment
   last.** Read them as a maintainer on the thread would: the package is
   what these two contain and quote, nothing else.

## Evidence gathering

For each check, pull the fact from the place named here and record it as
a quotable line before grading anything. Do not grade a check from
memory of the read-through.

1. **For `cause-grounded`:** copy out (a) the plan's stated cause, in its
   own words, and (b) each control run from the repro evidence with what
   it establishes. Then ask the one question that decides this check: does
   any control, or any stage the evidence pins the failure at, rule out
   the plan's cause? In live mode the repro evidence is the student's own
   posted repro comment on the issue; quote from it the same way.
2. **For `scope-bounded`:** copy out the plan's list of proposed changes
   as discrete items, and its not-in-scope line. Count the items that
   would still be needed if the only goal were fixing this issue's
   reported failure. Record any item that would not.
3. **For `executable`:** copy out the files or areas named, and the first
   concrete change proposed in them. Record whether that first step is a
   change or an investigation.
4. **For `test-plan-decisive`:** copy out the test plan, and beside it the
   repro evidence's steps and artifacts. Record the specific observable
   the test plan says will differ, or record that it names none.
5. **For `thread-aligned`:** copy out any maintainer-settled direction
   from step 3 of the read order, the policy line from step 4, and the
   plan comment's treatment of each. Record whether the comment
   acknowledges the direction, departs from it with a reason, or passes
   it in silence; and whether a disclosure line is present when the
   policy requires one.
6. **For the preferred checks:** copy out any explicit deferral with its
   reason, and any stated risk, unknown, or open question.

In live mode, gather the issue-side facts from the locations named in
`references/evidence-guide.md` before reading the drafts, keeping the
same order.

## Check execution

1. Execute the checks in this fixed order: `cause-grounded`,
   `scope-bounded`, `executable`, `test-plan-decisive`, `thread-aligned`,
   then the preferred checks. The order runs cheapest-to-decide first and
   never changes, so two executors reach the same grades in the same way.
2. Grade each check only against the evidence recorded for it in the
   previous stage. Do not re-read the whole package to grade a single
   check; if a needed fact was not recorded, go back and record it, then
   grade.
3. Apply the rubric's pass condition as written. If a check passes by its
   stated condition but feels wrong, grade it `pass` and note the tension
   in the summary. The fix belongs in the rubric, not in this run.
4. Grade `fail` when the evidence contradicts the pass condition. Grade
   `unclear` only when the evidence the check names is genuinely absent
   from the package — not when it is present and weak, and not when it
   was simply hard to find.
5. One exception, stated so it is not a judgement call: for
   `thread-aligned`, a package with no thread highlights and no policy
   requirement has no direction to walk past. Grade it `pass`, with the
   absence as the evidence line.
6. Each grade carries one evidence line: the quoted fact that decided it.
   A grade without a quotable fact is not finished.
7. Grade every check before assembling the verdict. Do not stop early on
   the first failure — the summary reports all five, because the author
   needs to know everything that is wrong, not just the first thing.

## Verdict assembly

1. Collect the grades for the five required checks. Preferred checks are
   set aside here; they never enter the verdict.
2. Convert `unclear` to `fail` for every required check, except where
   step 5 of check execution already resolved it to `pass`.
3. Apply the rubric's verdict rule: `accept` if and only if all five
   required checks are `pass`. Otherwise `reject`. There is no third
   verdict and no totalling — one failure is enough.
4. Name the deciding check. On a `reject`, the deciding check is the
   first failing check in the execution order from step 1 of check
   execution; quote its evidence line in the summary so the author knows
   where to start. On an `accept`, quote the preferred checks' grades
   instead, as the reasons to prefer this plan.
5. Emit the summary, then the fenced JSON block required by SKILL.md,
   with one entry per check — required and preferred alike — in execution
   order, and the verdict last. Nothing follows the JSON block.
