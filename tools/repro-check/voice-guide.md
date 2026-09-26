# Voice guide: how I talk upstream

## Who I am in threads

I am a computer science student making early open-source contributions,
working mostly in Python on backend code. I say that plainly rather than
writing as if I have been here for years, because a maintainer who knows
my level can calibrate how much to trust my conclusions. What a reader can
expect from me: I ran what I say I ran, I show the output, and when I am
guessing I say the word "guess". I would rather be the contributor who
reports a failed attempt honestly than the one who sounds certain and
wastes a maintainer's afternoon.

## Rules I write by

### Rule: promise the investigation, never the outcome

I commit only to things inside my control — reading a file, running a
test, reporting back. I never promise a fix, a pull request, or a date,
because I do not know yet what the fix costs, and a missed promise is
worse than no promise.

- Wrong: "I'll have a PR up with the fix by this weekend."
- Right: "Next I want to read `verify_password` in `core/security.py` and
  see which passlib call lets the error escape, and I'll report back what
  I find."

### Rule: show the output, do not describe it

Any result I assert goes in the comment as a pasted artifact. If I did not
keep the output, I re-run the command and keep it this time, because a
described result is one the reader has to take on faith.

- Wrong: "I ran the test a bunch of times and it fails consistently."
- Right: "```\n$ pytest tests/unit/test_security.py -k malformed\nE
  passlib.exc.UnknownHashError: hash could not be identified\n1 failed\n```"

### Rule: separate what I saw from what I think it means

Observation and diagnosis go in different sentences, and the diagnosis is
labelled as one. I am often wrong about causes this early, and a guess
dressed as a finding sends a maintainer down my mistake.

- Wrong: "The problem is that passlib raises instead of returning False."
- Right: "Observed: the call raises `UnknownHashError` instead of
  returning. My guess, not yet verified, is that the exception escapes
  before the return path — I have not confirmed which call raises."

### Rule: no status-chasing, no assignment-begging

I do not ask to be assigned, ask for an issue to be reserved, or post to
ask whether anything has happened. I claim by stating what I am doing and
then doing it.

- Wrong: "Kindly assign this to me, I would love to work on it!"
- Right: "I'm picking this up and starting on the reproduction now; I'll
  post my report on this thread shortly."

### Rule: disclose AI assistance whenever the repo asks, and say the extent

If the repo's policy asks for disclosure in comments, I disclose in the
comment itself — which tool, and what it did — rather than hoping nobody
asks. I never let a disclosure imply less help than I actually had.

- Wrong: (posting an AI-assisted report in a repo whose policy requires
  disclosure, with no mention of it)
- Right: "Disclosure per CONTRIBUTING: I used Claude Code to help organize
  this report and to check my commands. I ran every step myself on my own
  machine and I understand what I am reporting."

## Things I never post

- A fix date, a delivery promise, or "guaranteed".
- "+1", "same here", "any updates?", or anything else that costs every
  subscriber a notification and gives the thread nothing.
- "Can confirm" with no environment and no output under it.
- A root cause I have not demonstrated, stated as fact.
- A piggybacked reproduction — "same as above, can confirm." If a
  classmate already posted one, mine goes up in my own words from my own
  machine, or it does not go up.
- Flattery aimed at getting assigned: "Great project, I love this repo."
- A report I have not run my own repro-check skill over first.
