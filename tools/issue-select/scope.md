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

I come from data analytics: SQL-style thinking, data pipelines, and
pattern-spotting in messy data are where I am fastest. Python (3.11) is my
main language. I am newer to software engineering as a discipline than to
working with data, but I pick things up quickly and I would rather read real
code than a tutorial.

What I have actually built: a Telegram bot that pulls market data from a
third-party API, applies buy/sell rules to it, and posts a scheduled daily
message. That taught me external API integration, scheduling, and handling
the response that comes back empty, null, or in a shape I did not expect.
The next thing I want to add to it is a paid data source and an MCP
connection that can act on the signal instead of just alerting me.

What I want to get better at, specifically: the data contract between a tool
that fetches something and the code that consumes it — the key one side
writes and the other side reads. That is the seam where my own integrations
break, and it is the same seam in any agent or RAG system. More broadly:
agent tool-calling, retrieval and chunking, and the guardrail layer around
an LLM. Reading and extending an unfamiliar Python codebase is the muscle I
am here to build.

Rank higher for me: Python backend issues in the agent, RAG, ingestion, or
safety subsystems; anything where a tool and its consumer disagree about the
data passed between them; bugs with a concrete reproduction I can run
locally; parsing, data-quality, and edge-case handling.

Rank lower for me: React and CSS frontend work, and multi-day infrastructure
or CI build-outs. I want a bounded fix I can finish and understand end to
end, not a second project.
