# Evidence guide: where proof lives in a reproduction package

The map the rubric's checks read. For each proof family: where to find it, and what
good looks like when you do. Thresholds are observable conditions, not adjectives,
because a check nobody else can execute is a check that does not work.

A note that applies throughout: **grade the proof, not the polish.** Length,
headings, and formatting are not evidence. A four-line report with an environment
line, exact commands, and an output excerpt has everything. A thousand-word report
with no artifact has nothing.

## Environment

**Where it lives.** In an eval bundle: the repro report's environment section, or an
inline "Environment:" line, usually its first line. In live mode: the same place in
the draft comment. The issue side is in the issue body and the repo-facts block,
which is where you find the version and platform the issue targets.

**What good looks like.** One line is enough if it carries two things: the platform,
and the version or commit of the software under test. "yq 4.53.3 (Homebrew), macOS
15.5 (arm64)" is complete. Where the issue's behaviour depends on a further setting,
that setting is part of the environment: the driver on a minikube issue, the shell on
a prompt issue, the build profile where debug and release fail differently, the
install method where packaging is implicated. Absent entirely is a fail. A version
that differs from the issue's target is **not** a fail here; it becomes evidence for
`artifact-matches-issue`, where what matters is whether the report said so.

## Steps

**Where it lives.** The repro report's steps or commands block, read together with
whatever the package supplies alongside it: input file contents, config, a snippet.
In live mode, also the issue's own reproduction steps, to see whether the draft
follows them or departs from them.

**What good looks like.** A stranger on the thread, with no access to the author's
machine, could run them start to finish. Concretely: every input is either printed in
the package (a `printf` that writes the fixture, the YAML inline, the config shown)
or publicly fetchable (a released version, a public repo). The steps start from a
stated starting point and end at the trigger the issue names.

The failure modes to look for are specific. Steps that reference a private repository
or an unshared config are unrunnable no matter how precise they read. Steps that omit
a setting the behaviour depends on leave the reader unable to place the attempt even
if they can run the commands. And steps that quietly alter the issue's trigger, a
different flag, a different syntax, a different expression, are runnable but are no
longer about this issue; that deviation is graded under `artifact-matches-issue`.

## Behavior shown

**Where it lives.** The artifacts: fenced output blocks, log excerpts, error text,
exit codes, screenshots, measurements. In an eval bundle these sit inside the repro
report. The comparison side is the issue's description of what goes wrong.

**What good looks like.** The artifact is output from a real run, pasted, not
described. "I confirmed the crash" is an assertion. A traceback is an artifact.

For an artifact to show *this* issue's behaviour rather than an adjacent one, put the
two side by side and ask what failed, not whether something failed. These are the
substitutions that read as proof and are not:

- A graceful validation error (clean message, exit 1) standing in for a reported
  crash or panic (exit 101, a stack trace).
- A compile or parse error standing in for a runtime error, usually because the
  input was retyped slightly wrong.
- Output corruption standing in for a process dying, the program is visibly still
  alive in the artifact.
- An artifact that only shows the software running at all: a version banner, a
  session list, a UI screenshot with nothing wrong in it.
- The right failure on the wrong version, where the issue is confirmed on latest and
  the attempt ran on an old release.

A control run, the same steps on an input or setting where the behaviour does not
occur, is the strongest single thing a report can add, because it shows the boundary
of the bug and not just its presence.

## Honesty

**Where it lives.** The seam between what the report asserts in prose and what its
artifacts actually show. Read the prose claims first, then check each one against the
evidence underneath it.

**What good looks like.** Every claim in the prose is backed by something shown, and
claims the evidence does not support are absent or marked as hypothesis. Three
patterns, graded differently:

- **Faithful reproduction.** The report says it reproduced, and the artifact shows
  the issue's behaviour. Pass.
- **Honest cannot-reproduce.** The report says it could not reproduce, shows the real
  attempt, and names what differed from the issue's environment or setup and why that
  might matter. **This is a pass.** It is a genuine result, and reporting it
  faithfully is what the reproduction phase is for.
- **Confident wrong-target.** The report says it reproduced, the artifact shows
  something else, and the prose narrates it as the issue's behaviour. Fail, and the
  more confident the narration, the worse it is: certainty asserted over the wrong
  artifact is what wastes a maintainer's time.

Root-cause claims are held to the same standard as reproduction claims. "I verified
this is a debounce race" needs shown evidence of the race, not a plausible story.
Naming a hypothesis as a hypothesis is fine and useful.

## Comms

**Where it lives.** The claim comment, read against the issue it belongs to. The
repo-facts block's contribution-policy line, plus any stated issue or comment
template. In live mode: `CONTRIBUTING.md` in the repo root or `.github/`, the docs it
links to, any `AI_POLICY.md`, and the issue and PR templates.

**What good looks like, for the claim comment.** Two properties, both testable.
*Specific:* it names something particular to this issue, the symptom, the file or
component, or what will be investigated, so that pasting it unchanged onto a
different issue would read as wrong. *Modest:* it promises investigation and a
report, not an outcome. "I will look at whether X carries the fix and report back" is
modest. "I'll have a PR up in two days" promises something the author cannot
guarantee, and a claim that over-promises is the most common comms failure in
otherwise fine packages.

The boilerplate tells are worth naming: "please assign me", "I'd like to work on
this" with nothing else, a bare +1 or me-too, and any stated deadline for a fix.

**What good looks like, for policy.** An outright ban on AI-assisted contributions is
a hard stop. A **disclosure requirement** is the case to watch: where the repo's
stated policy says AI assistance must be disclosed, the comments have to disclose it,
and course packages count as AI-assisted work. Other conditions, disclose, review,
understand, test, are terms to follow rather than reasons to stop. Silence passes: a
repo that states nothing about AI has not set a requirement, and reading silence as
prohibition would reject most repos on the internet.
