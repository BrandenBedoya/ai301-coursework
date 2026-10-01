# Procedure: how this skill grades a plan package

These are steps for grading a plan someone else wrote, not for writing one. Follow
them in order. Every location named below is defined in
`references/evidence-guide.md`.

## Read order

1. In live mode only, read `scope.md` first. Confirm the issue URL is in the scoped
   repo; if it is not, or the `Repo:` line still holds a placeholder, stop and say so.
   Note the house rules. In eval mode skip this step.
2. Read `rubric.md` and `references/evidence-guide.md`. Write down the six required
   check names, the two preferred check names, and the verdict rule.
3. Read the issue (title and body) and note, in one line, the behavior it reports.
4. Read the repro evidence **before** the plan. List every numbered step and every
   control run, and next to each one write what it shows in one line, especially what
   each control rules in or out. This list is the yardstick for the plan's diagnosis,
   so it has to exist before you read the plan's own story about the cause.
5. Read the thread highlights. For each comment, note the author's role. For every
   OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR comment, write one line on the
   direction it gives, if any (culprit named, fix agreed or rejected, patch or test
   build posted, PR opened, behavior called intended).
6. Read the repo-facts block's contribution-policy line and note, word for word, any
   sentence about AI use, disclosure, or who must write comments.
7. Read the candidate plan, then the candidate plan comment, in full.

## Evidence gathering

1. For `diagnosis-fits-evidence`: copy the plan's stated cause as one quoted line.
   Copy the change the plan proposes as one line. Pull your step-and-control list from
   Read order step 4.
2. For `one-bounded-change`: list every distinct change the plan proposes, one per
   line, from its scope statement, proposed-changes list, approach steps, and files.
   Mark each line as one of: core fix, same fix at a sibling site, regression test,
   audit-and-report, deferred or split out, or extra work.
3. For `stranger-can-start`: copy the named file, function, or code path, and the one
   approach the plan commits to there. Copy any phrase that leaves a choice open or
   places the change "somewhere".
4. For `test-plan-observable`: copy the test plan's run (which command or steps) and
   its expected result, word for word.
5. For `follows-thread-direction`: take the maintainer-direction lines from Read order
   step 5. For each, search the plan comment (then the plan) for a sentence that
   follows it, names it, or explains a difference. Copy that sentence, or record
   "none".
6. For `ai-disclosure-where-required`: take the policy sentence from Read order step 6
   and copy any sentence in the plan comment that discloses AI use, or record "none".
7. For the preferred checks: copy the plan's risk or open-question lines, and any test
   the plan says it will add.
8. Live mode: the issue, thread, and repo facts come from GitHub as the evidence guide
   says; the repro evidence is the student's posted repro comment as quoted in the
   drafts; the plan and comment are the drafts. Grade only what the drafts contain.

## Check execution

1. Run the required checks in rubric order, then the preferred checks.
2. For each check, hold the gathered evidence against that check's pass condition in
   `rubric.md` and grade `pass`, `fail`, or `unclear`. Grade from the pass condition
   as written, not from whether the plan feels good or bad overall.
3. `diagnosis-fits-evidence`: walk your step-and-control list one item at a time and
   ask "could this result happen if the plan's stated cause were the cause?". One
   "no" is a `fail`; record that step as the evidence. A stated cause that merely
   repeats the issue or the thread still has to survive this walk.
4. `one-bounded-change`: any line marked "extra work" in Evidence gathering step 2 is
   a `fail`, even when the core fix is right. Name the extra item in the evidence.
5. `stranger-can-start`: `fail` if no location is named, or if a copied open-choice
   phrase covers what the change does or where it goes.
6. `test-plan-observable`: `fail` if the copied expected result is a feeling or a
   general statement, or if the only run is "the test suite passes".
7. `follows-thread-direction`: `pass` if there were no maintainer-direction lines.
   Otherwise `fail` if any direction line came back "none".
8. `ai-disclosure-where-required`: `fail` only if the policy sentence requires
   disclosure in comments or for all AI use, and the disclosure search came back
   "none". Every other case is `pass`.
9. Grade `unclear` only when the evidence the check needs is genuinely missing from
   the package (for example, no test plan anywhere), not when it is weak. Weak
   evidence is graded against the pass condition like anything else.
10. A check may be graded from the evidence gathered for it without re-reading the
    whole package. Re-read only the part the evidence guide names for that check.
11. Grade every check on every package, even after a required check fails, so the
    output shows all the gaps at once.

## Verdict assembly

1. Apply the verdict rule in `rubric.md`: `accept` only if all six required checks are
   `pass`; any required `fail` or `unclear` is `reject`.
2. Ignore the preferred checks for the verdict. Report them anyway.
3. In the readable summary, put one line per check, and for a `reject`, name the
   deciding required check first with the quote or step that failed it.
4. In live mode, after the verdict, hold the draft comment against `voice-guide.md`
   and list any rule it breaks, quoting the rule. This never changes the verdict.
5. Emit the JSON block from `SKILL.md` last, with one entry per check and each
   evidence field holding the one-line fact or quote that decided it.
