# Evidence guide: where proof lives in a reproduction package

The map the rubric's checks read from. For each family: where to look,
and what a passing example looks like when you get there.

## Environment

**Where it lives.** In an eval bundle: the line or block at the top of the
repro report that names versions, usually opening with `Environment:`.
Read it against two other places in the same bundle — the issue context,
which states the version the bug was filed against, and the repo-facts
block's `bug reports:` line, which lists what the repo's template asks
reporters to supply. In live mode: the draft report's own environment
line, against the issue's body on GitHub and the template in
`.github/ISSUE_TEMPLATE/`.

**What good looks like.** The tool's version and the operating system or
platform both appear, in a form the reader could match to their own
machine — `bat 0.26.1 (installed via cargo), Fedora 44 (x86_64)`. When the
version tested is not the version the issue names, the report says so
itself: *"The issue was filed against 13.0.0; behavior is unchanged on
15.2.0."* That sentence is what turns a mismatch into information. A
report that silently tests an older version than the issue targets leaves
the maintainer unable to place the result. Install method and toolchain
version help and are worth recording, but the version and the platform are
the two that decide this family. Hardware specifications are not an
environment record: RAM and GPU model say nothing about which build ran.

## Steps

**Where it lives.** The repro report's steps or reproduction section, and
any fenced block inside it holding commands. In an eval bundle the steps
sit between the environment line and the first output excerpt. In live
mode, the same section of the draft.

**What good looks like.** A stranger on a clean machine could execute the
steps in order without asking a question. Every step is a literal command,
a file whose contents are shown, or a configuration quoted inline —
`printf 'foo: bar\n' > some.yml`, not "create a small YAML file". Where
the input is long, pointing at the issue's own input ("the exact 12 lines
from the issue") is enough, because the reader has that. The failure mode
to watch for is a step the reader cannot obtain at all: a private company
monorepo, an internal block list, a build that is not public. Such a
report may be entirely honest and still prove nothing to anyone else,
because nobody can run it. Prose padding around real commands is fine;
prose standing in place of a command is not.

## Behavior shown

**Where it lives.** The fenced output blocks in the repro report — terminal
excerpts, log lines, console errors, exit codes — read against the failure
the issue describes in its own body and output blocks. The issue's blocks
are the reference; the report's are the candidate.

**What good looks like.** The two show the same failure. Matching means
the same kind of failure, not merely that both are unhappy: a panic with a
stack trace, a handled error message, a non-zero exit, and correct-looking
but wrong output are four different outcomes. `capacity overflow` at exit
101 is not the same result as `Invalid value for '--line-range'` at exit 1,
even though both came from the same flag. When the issue and the report
both print an exit code, the codes match. Two traps live here. The first
is the adjacent bug: the author retyped the input or the flag slightly, hit
a different code path, and reported the resulting error as confirmation.
Compare the command in the report against the command in the issue
character by character before believing the artifact. The second is the
setup screenshot: output proving the environment launched, the tabs
appeared, the session started, offered as proof of a failure that is never
actually shown. Setup is not behavior.

An honest cannot-reproduce belongs in this family too and passes it. The
shape: the report says plainly that it did not reproduce, shows the
artifact of what happened instead, and names what differed from the
issue's conditions — a different OS, a different shell, a limit it could
not force. That is a real experimental result and is worth posting.

## Honesty

**Where it lives.** Wherever the text makes a factual assertion: the claim
comment's summary of what the author has done, the report's opening
sentence, and its `Actual:` and conclusion lines. Read each assertion
against the artifacts in the same package, and against nothing else.

**What good looks like.** Every outcome asserted has something under it.
The tells run the other way — a sentence describing a run that no artifact
shows is the most common, and it is invisible unless you look for it:
"I ran this ten times", "confirmed on two separate machines", "I also
confirmed the same failure on the older version". Each of those is a
result being claimed without evidence, and each should be graded the same
way whether or not the rest of the report is solid. The second tell is the
diagnosis with no demonstration: a report that names a race condition, a
cancelled debounce, a specific function as the root cause, while showing
no artifact that isolates it. A theory is worth posting when it is labelled
a theory; it fails when it is labelled a finding. The third is confidence
that outruns the artifact directly above it — "verifiably broken",
"conclusively demonstrates", "100% confirm" — sitting over output that
shows something else. Adjectives are not the problem; adjectives
contradicted by the adjacent evidence are.

## Comms

**Where it lives.** Two places. The repo-facts block carries the repo's
stated asks: the `bug reports:` line for the template's requirements, and
the `contribution policy:` line for the contribution guide and any AI
policy. The candidate claim comment carries the words being judged against
them. In live mode: `CONTRIBUTING.md`, any `AI_POLICY.md` or
`AI_USAGE_POLICY.md` it links, the issue templates in `.github/`, and the
draft comment.

**What good looks like, on conventions.** The comments supply what this
repo asks for. Template asks are specific and checkable — when a template
requires confirming the bug on the latest release and on the main branch,
a report tested on a years-old version has not met the ask, however clean
its artifact. AI policies vary in exactly the way that matters here, so
read the scope of the requirement rather than the presence of the word AI.

Start from a fact about the author: every package graded here is written
by a contributor working with AI assistance. So a policy that requires
disclosure always applies, and the question is never "is there evidence
this person used AI?" — assume they did. The question is only what this
repo asks, and whether the comments do it. Some repos require disclosure
of any AI assistance, in any comment, naming the tool and the extent; a
comment with no disclosure line fails, and that holds even when the
reproduction underneath is immaculate, because the policy is a wall rather
than a quality bar. Others accept AI-assisted work while requiring only
that a human understand it, or ask for disclosure in pull requests while
explicitly stating no ask for issue comments; those require nothing of a
claim comment. A policy that comments be written by a human in their own
words is a voice requirement, not a disclosure requirement, and comments
that read as the author's own satisfy it. Most repos say nothing at all,
and silence is not a restriction.

**What good looks like, on the claim itself.** The claim names something
only a reader of this issue could name — the specific symptom, the version
tested, the function the thread pointed at — and commits to an
investigation rather than an outcome. Promising a fix, a date, or "within
2 days guaranteed" commits to something the author cannot control and that
the maintainer never asked for. Asking to be assigned, or for the issue to
be reserved, puts work on the maintainer in exchange for nothing. At the
other end, a comment with no content at all — "+1 also seeing this", "any
updates?" — costs every subscriber a notification and gives the thread
nothing. Between those, specific and bounded is the target: here is what I
saw, here is what I will look at next.
