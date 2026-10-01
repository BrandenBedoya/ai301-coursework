# Evidence guide: where evidence lives in a plan package

The map for every check in `rubric.md`. Each family says where to look in an eval
bundle, where to look live, and what good looks like there.

## Diagnosis and grounding

**Where it lives.** Bundle: the cause is the candidate plan's `Diagnosis` line or
section (sometimes the first sentence under `Candidate plan`, or a `Problem statement`
or `Background` heading). The behavior it must explain is the `Repro evidence` block:
its numbered steps, pasted output, and every `Control` run. Live: the cause is in the
draft `plan.md`; the behavior is the student's posted repro comment on the issue,
quoted in the drafts.

**What good looks like.** The stated cause predicts every result in the repro block,
including the controls. Controls are the sharpest test: if a control shows the blamed
component working (the same items parse without the flag, the same build prints a
friendly error elsewhere, the same operator synthesizes null at top level), or shows
the damage already done before the blamed step (the zeros are already gone in the
parsed table), the diagnosis is ruled out no matter how confident it reads. Good
diagnoses often cite the control that supports them ("both controls in the repro
fit"). The change then acts at that cause, not around it.

## Scope

**Where it lives.** Bundle: the plan's `Scope` line, `In scope` / `Not in scope`
lines, any numbered `Proposed changes` list, the `Approach` steps, and the `Files`
or `Files and areas` list. The plan comment usually restates the scope in one
sentence. Live: the same parts of `plan.md`.

**What good looks like.** One change that makes the reproduced behavior correct,
plus its regression test, with a not-in-scope line naming the tempting adjacent work
(a rework the thread discussed, a second symptom) as deferred or split out. A
drive-by rewrite reads differently: phrases like "while in there", "while touching",
"fix the pipeline properly", "rather than spot-fix", "the class rather than the
instance", and lists that add migrations, upgrades, new options, module splits, or CI
matrices on top of the core fix.

## Executability

**Where it lives.** Bundle: the plan's `Files` list, `Change` or `Approach` steps,
and any named function, file path, or code path inside the diagnosis. Live: the same
parts of `plan.md`.

**What good looks like.** A named file or function plus one committed mechanism
("clamp the fill with `saturating_sub` at the two subtraction sites in
`src/printer.rs`", "append `--` before the file path in
`crates/cli/src/decompress.rs`"). Naming an unknown inside a located area is fine
("the exact clamp site may be one layer up or down"). A plan that a stranger cannot
start reads as investigation: "profile", "look into", "figure out what changed",
"not sure which layer", "somewhere", "whichever is easier", "fix it once the cause is
clear".

## Test plan

**Where it lives.** Bundle: the plan's `Test plan` or `Test` line, read next to the
repro block's commands and recorded output. Live: the test plan in `plan.md`, read
next to the posted repro comment's steps and output.

**What good looks like.** The repro re-run with the post-fix result spelled out so
it can be checked against real output: "expect drawn output and exit 0", "at step 3
the color must flip without leaving the view", "the three repro commands print
`[[0,1,2,3,5,7,8,9]]`", "5 reattach cycles with no rgb strings". Controls re-run
unchanged is a bonus. A vague plan names a feeling ("should feel fast", "nothing else
should feel broken") or only the suite ("run `cargo test --workspace` and make sure
nothing regresses"), which says nothing about the reproduced bug.

## Honesty

**Where it lives.** Bundle: `Risk`, `Risks`, `Unknowns`, or `open question` lines in
the plan, the plan comment's flagged questions, and stated deferrals in the scope.
Live: the same parts of `plan.md`, plus its `## Deviations` section once the build is
done, which records what changed from the posted plan and why.

**What good looks like.** A specific risk tied to the change ("`\` is a legal
filename character on Unix, so the normalization is gated to Windows"), or an
unknown labeled as an unknown ("I have not yet verified which layer clamps the
viewport"). False confidence reads as "the root cause is", "red herring", "should be
a small PR", or "has always been" with nothing in the repro block behind it. After a
build, an honest deviation is written in `## Deviations`; a change that exists only
in the diff is not honest.

## Comms

**Where it lives.** Bundle: the `Thread highlights` list (each line gives the date,
author, and role in parentheses) read against the `Candidate plan comment`; and the
`Repo facts` block's `contribution policy` line read against the same comment. Live:
the issue's comment thread on GitHub (author association shown on each comment), the
repo's `CONTRIBUTING.md` and any `AI_POLICY.md`, and the draft `comment.md`.

**What good looks like.** Thread-aware: when an OWNER, MEMBER, COLLABORATOR, or
CONTRIBUTOR has pointed at the culprit, settled or rejected a fix, posted a patch, or
opened a PR, the comment says so and either follows it or explains the difference
("along the lines already agreed here", "I have read #3543 and #3548", "follows the
direction proposed here"). Boilerplate proceeds as if the thread were empty, for
example a docs-only plan when the owner has already named the code at fault and asked
for testing of a patched binary.

On policy, separate two kinds of rule. A **disclosure** rule ("all AI usage in any
form must be disclosed", AI use stated in issues or comments) needs a disclosure
sentence in the comment itself. An **authorship** rule ("comments must be written by
humans in their own words") or a PR-only disclosure ask does not require the comment
to disclose anything. A policy that is silent on AI requires nothing.
