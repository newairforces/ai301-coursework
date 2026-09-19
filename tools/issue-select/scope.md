# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s1`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

Python is the language I have actually shipped in: Flask apps with
WTForms and JSON endpoints for coursework, plus SQL and pandas-style data
work. I have read enough FastAPI, SQLAlchemy and Alembic to find my way
around the Path Review backend, and I can work in TypeScript and React on
the frontend, though I am slower there and lean on the existing component
patterns rather than inventing new ones.

What I want to get better at is backend API work and the retrieval and
safety layers — anything that makes me read a real request path end to
end rather than only touch styling. I would rather take a bug with a
reproduction I can run locally than an open-ended feature.

What I want to avoid: Docker or CI infrastructure changes I cannot verify
on my own machine, model-prompt tuning with no objective test, and
anything whose acceptance criteria are a matter of taste. I have roughly
six to eight hours for this contribution, so a single-file or
single-module change with a test I can point at fits the time I have.
