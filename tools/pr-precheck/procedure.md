# Procedure: how this tool grades a PR package

Operating steps. Follow them in order, exactly as written. Where a step
cannot be completed because the package lacks the evidence it names,
record that absence and carry it into the check rather than guessing.

## Read order

Read the whole package before grading anything. Five of the six
required checks are side-by-side comparisons, and a comparison is only
honest if the reference side is written down before the candidate side
is read. The candidate PR's description is read LAST, because it is the
part most likely to tell you what to think ("implements the plan
exactly"), and it must be checked against the diff, not used to read
the diff.

1. **Issue context first** (eval: the Issue section; live: `gh issue
   view <URL> --comments`). Note in one line the failure the issue
   reports.
2. **Repo facts second** (eval: the Repo facts block; live:
   `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`).
   Write down, as a numbered list, every stated PR ask: each template
   section, each checklist item, each required link (`closes #`), each
   required artifact (CHANGELOG, whatsnew, i18n, cheatsheets, tests),
   each named test or check command, and the AI-use policy verbatim
   with whether it covers pull requests. If there are none, write
   "no stated asks". Live mode: add the course's AI-use disclosure in
   Notes for Reviewers to this list.
3. **Thread highlights third.** Note any maintainer question, caveat,
   or settled direction.
4. **Plan context fourth** (eval: the Plan context block; live:
   `plan.md` including `## Deviations`). Write down, before looking at
   the PR: (a) the files the plan names; (b) each deliverable as a
   separate item (a plan with "(1) a warning and (2) a docs update" has
   two); (c) the not-in-scope line verbatim; (d) every deviation or
   deferral note; (e) the test plan's reproduction(s), with the exact
   input or condition that triggers the failure and the expected-after,
   one line per case, each tagged FAILURE MODE (the bug misbehaved
   there before the fix) or NEW BEHAVIOR (something the fix adds, such
   as a warning, or a control that must stay unchanged); (f) any suite the test plan says to run.
5. **The diff fifth** (eval: the Diff section; live: `git diff
   main...HEAD`, with `--stat` for the file list). Then the commit list
   (live: `git log --oneline main..HEAD`).
6. **The test evidence sixth** (eval: the Test evidence section; live:
   `test_evidence.md`).
7. **The title and description last** (eval: Title and Description;
   live: `pr_draft.md`, first line is the title).

## Evidence gathering

For each check, pull the facts from the place named here and record
them as quotable lines before grading anything. Do not grade from
memory of the read-through.

1. **For `diff-matches-plan`:** list every file in the diff and, under
   each, every hunk in one line ("adds `resolve_symlinks` config
   field", "re-indents lines 40-60", "adds test for escaped space").
   Beside each hunk write which item from read-order step 4 it traces
   to (a), (b), (d), or a repo-required artifact from step 2, or write
   UNTRACED. Then walk the plan's deliverables list (4b) and mark each
   PRESENT, NOTED-AS-LEFT-OUT (quote the note), or MISSING. Separately,
   check whether any hunk does something the not-in-scope line (4c)
   excludes.
2. **For `description-matches-diff`:** list each claim the description
   makes about the change (what it does, what files or docs it
   touches, any fidelity claim like "exactly as planned" or "no other
   changes"). Beside each claim write CONFIRMED (name the hunk) or NOT
   IN DIFF. Then list any behavior-changing hunk the description never
   mentions.
3. **For `repro-rerun-decisive`:** for each failing case from read-order
   step 4e, find the matching run in the test evidence. Record: the
   command or steps run, whether they use the same triggering input or
   condition as the repro (same file layout, same request method, same
   shell, same delay), the after-output quoted, and whether a before is
   shown or quoted from the repro evidence. A FAILURE MODE case with no
   matching run and output is recorded NOT RE-RUN. A NEW BEHAVIOR case
   is recorded MET when the evidence states its result or names a test
   covering it, and NOT RE-RUN only when it is never mentioned. A run
   that uses the control input or a different path instead of the
   failing one is recorded WRONG PATH.
4. **For `repo-checks-run`:** take every test or check command from
   read-order steps 2 and 4f. For each, find it in the evidence or
   description and record the command as written and its stated
   outcome, or record NOT SHOWN. Record "all tests pass" with no
   command as NOT SHOWN.
5. **For `diff-clean`:** scan every added line (`+`) and removed line
   (`-`) for: debug output, commented-out code, dead or `_unused`
   functions, `allow(dead_code)`, new TODO/FIXME, whitespace or indent
   changes on otherwise-untouched lines, import reordering, and
   standalone blocks of lines removed and re-added unchanged. Before
   recording a `-`/`+` pair of identical lines as a hit, check whether
   it sits inside a statement whose neighboring line the fix changes;
   if so, it is a rendering artifact, not a hit. Quote each hit with its file. Note
   commit messages that signal the same ("wip", "debugging").
6. **For `standards-met`:** walk the numbered list from read-order
   step 2. For each ask, record MET (quote the description line or name
   the diff file), MARKED N/A (quote the reason), or MISSING. For the
   AI policy, record whether it requires PR disclosure and whether the
   description has a disclosure naming the tool and extent.
7. **For the preferred checks:** pair each maintainer question from
   read-order step 3 with the description sentence that engages it, or
   NONE; and note each commit message.

In live mode, gather issue-side facts with the commands named in
SKILL.md, and the working-copy facts from the files named in
`references/evidence-guide.md`, keeping the same order.

## Check execution

1. Execute the checks in this fixed order: `diff-matches-plan`,
   `description-matches-diff`, `repro-rerun-decisive`,
   `repo-checks-run`, `diff-clean`, `standards-met`, then
   `thread-engaged` and `commits-focused`. This is rubric order and it
   never changes.
2. Grade each check only from the lines recorded for it in evidence
   gathering. If a needed fact was not recorded, go back, record it,
   then grade. Do not re-read the whole package to grade one check.
3. Apply the rubric's pass condition as written:
   - `diff-matches-plan` fails on any UNTRACED hunk that changes
     behavior, any MISSING deliverable, or any hunk the not-in-scope
     line excludes. UNTRACED debris (debug lines, churn) is graded by
     `diff-clean`, not here.
   - `description-matches-diff` fails on any NOT IN DIFF claim, or any
     fidelity claim contradicted by an UNTRACED hunk.
   - `repro-rerun-decisive` fails on any NOT RE-RUN or WRONG PATH case,
     or a run with no quoted after-output.
   - `repo-checks-run` fails on any NOT SHOWN command.
   - `diff-clean` fails on any quoted hit.
   - `standards-met` fails on any MISSING ask.
4. Grade `unclear` only when the evidence the check names is genuinely
   absent from the package (for example, no test-evidence section at
   all), not when it is present and weak. Weak evidence is graded
   against the pass condition like any other.
5. When the thing a check reads for does not exist (no stated asks for
   `standards-met`, no named suite for `repo-checks-run`), grade `pass`
   with the absence as the evidence line, as the rubric directs.
6. If a check passes by its stated condition but feels wrong, grade it
   `pass` and note the tension in the summary.
7. Each grade carries one evidence line: the quoted fact that decided
   it (a hunk, a description sentence, an output line, a missing ask).
8. Grade every check before assembling the verdict. Do not stop at the
   first failure; the author needs everything that is wrong.

## Verdict assembly

1. Collect the six required grades. Set the two preferred grades aside;
   they never enter the verdict.
2. Convert every remaining `unclear` on a required check to `fail`.
3. Apply the rubric's verdict rule: `accept` if and only if all six
   required checks are `pass`; otherwise `reject`. No totalling, no
   third verdict.
4. Name the deciding check. On a `reject`, it is the first failing
   required check in execution order; quote its evidence line in the
   summary so the author knows where to start. On an `accept`, quote
   the preferred checks' grades instead.
5. Emit the summary, then the fenced JSON block from SKILL.md with one
   entry per check (required and preferred) in execution order, and
   the verdict. Report the grades as executed (`unclear` stays
   `unclear` in the JSON; the conversion in step 2 only feeds the
   verdict). Nothing follows the JSON block.
