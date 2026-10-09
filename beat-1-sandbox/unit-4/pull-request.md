# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/109

**Branch**

`fix/72-verify-password-fail-closed`

## Eval iterations

**Run history**

1. Full run, first draft of all four files: **18/20** (pkg-02 and pkg-05 graded reject against gold accept; categories clear-accept 5/7, every other category full). That already cleared the bar, but both misses were clear accepts my rubric was too strict on, so I revised.
2. Partial run, `--only pkg-02,pkg-05,pkg-18,pkg-07,calib-04 --include-calibration`, after loosening `diff-clean` and `repro-rerun-decisive`: **4/4** scored (pkg-02 and pkg-05 flipped to accept; canaries pkg-18, pkg-07 and calib-04 stayed reject). Partial, so no bar verdict.
3. Full confirming run with `--save-run eval-run.txt`: **20/20** (clear-accept 7/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3). This is the run in `eval-run.txt`.

**Package analysis**

**pkg-02** (Textualize/rich#4208, clear-accept). Gold: **accept**. My rubric on run 1: **reject**, failed on `diff-clean`. On run 3: **accept**.

The fix changes `clear=clear` to `clear=False` inside multi-line `export_html(...)` and `export_svg(...)` calls. The diff renders the whole argument list as removed and re-added, so lines like `html = self.export_html(` and `theme=theme,` show up as identical `-`/`+` pairs. My run-1 `diff-clean` said it "fails on ... lines deleted and re-added identically", and the grader read exactly that: "lines like `html = self.export_html(`, `theme=theme,` ... are removed and re-added identically." By the letter of my check that was right. But those lines aren't churn. They're how a unified diff shows a statement where one argument changed, and the author didn't touch them. Every other check passed, including the CHANGELOG entry the template asks for. So the rubric was wrong, not the package. I narrowed the rule to "a standalone block of lines deleted and re-added identically" and wrote the rendering artifact out explicitly as not churn. After that, the run graded it accept.

**Check rationale**

> | `diff-clean` | The unified diff's added and removed lines, and the commit list. | The diff contains only the change and what supports it. Fails on any of: a debug print or log line left in (`eprintln!("DEBUG`, `print(`, `console.log`, `dbg!`); commented-out code (an old attempt or a disabled line); a dead function or `allow(dead_code)`/`_unused` helper; a new TODO or FIXME; formatting, re-indent, or import-reorder churn on lines the fix does not otherwise touch; a standalone block of lines deleted and re-added identically. Unchanged lines that reappear as `-`/`+` pairs inside a multi-line statement whose other line the fix does change (an argument list where one argument changed) are a diff rendering artifact, not churn. Commit messages like "wip", "fix", "misc cleanups while debugging" are supporting evidence, not a failure on their own. A comment or docstring that explains the fix is not debris. | required |

It reads as a list of concrete tells, not "the diff is clean", so two graders scanning the same `+`/`-` lines land in the same place. Each tell comes from the unreviewable packages: the `DEBUG` eprintln and dead `count_open_fds` in pkg-15, the commented-out attempt and `_unused_buffer_probe` in pkg-12, the re-printed lines in pkg-18. The "standalone block" wording and the rendering-artifact sentence are the run-1 revision described above. The original "lines deleted and re-added identically" was catching pkg-02's argument lists. I deliberately did not make commit messages a failure on their own. "wip"/"fix" commits always come with real debris in these packages, and a check that fails on commit wording would be grading the write-up's shape rather than the diff. I added the docstring sentence because a fix that explains itself (like my own `except ValueError` comment) shouldn't read as debris.

**Trade-offs**

Narrowing "identical re-added lines" to standalone blocks means a diff that churns *inside* a statement it also edits, re-wrapping an argument list for no reason, now passes `diff-clean`. I accept missing that, since a reviewer can still see the real change on that line. The risk was pkg-18, whose debris includes "two blocks of identical re-printed lines", so I added it as a canary to the `--only` run. It stayed reject, because its blocks are standalone and it also has an import-restructure hunk and a commented-out `eprintln`. For the other loosening (new-behavior cases in a test plan can be met by a stated result or a named test instead of pasted output), I canaried pkg-07 and calib-04. Both stayed reject, because their skipped cases are failure modes of the bug, which still need pasted output. The confirming full run then showed no flips anywhere (20/20).

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
