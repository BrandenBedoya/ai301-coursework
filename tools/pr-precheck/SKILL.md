---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one package: is this pull request ready to
submit? A package consists of the PR title and description, commits,
diff, and test evidence, read against the issue and the accepted plan
the PR claims to implement. Do not grade the merits of the issue, the
author, or work outside that package.

## Inputs and modes

Use exactly one mode per run.

- **Live mode:** after checking the scope seam, read the issue URL
  named by the user, including its thread; `plan.md`, including any
  `## Deviations` section; the committed branch diff from the repo
  root using `git diff main...HEAD`; the title and description in
  `pr_draft.md`; and the captured outputs in `test_evidence.md`.
  Read the scoped repository's `.github/PULL_REQUEST_TEMPLATE.md` and
  `docs/CONTRIBUTING.md`, plus any AI policy those sources identify.
  Compare the PR to the plan and issue, not to assumptions about what
  a good change usually looks like. If the instructor routed the
  student to a house chain, use its house plan and reproduction pack
  instead of student-authored plan/reproduction files, and state which
  artifacts were used.
- **Eval mode:** read only the single frozen package bundle supplied
  by the harness. The bundle is the whole world: do not fetch the
  issue, repo, policy, or any other file. Grade every rubric check
  and assemble the verdict using the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before any other package artifact. If
the `Repo:` line is still a placeholder, refuse to grade and tell the
student to fill it in; never guess the repository. Compare the issue
URL's owner and repository with that line. Refuse to grade an issue
outside the scoped repository, and apply the listed house rules when
reading an in-scope package. In eval mode, ignore `scope.md`.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` and compare the outgoing PR title
and description with its rules. Before the JSON block, report each
broken rule by quoting it and naming the text that conflicts. Voice
notes do not change the verdict unless a rubric check independently
grades the same issue. In eval mode, ignore `voice-guide.md`.

## Component reads

Read `rubric.md` for the named checks, their evidence, pass conditions,
weights, and verdict rule. Use
`references/evidence-guide.md` to locate and interpret evidence, then
execute `procedure.md` in its stated order. Do not invent a missing
step: report a procedure gap before the JSON block. If the rubric has
no authored checks or the procedure has no authored steps, refuse to
grade and explain which component is empty. Treat template comments
as instructions, not as authored checks or steps.

## Verdict and output

The verdict is binary: `accept` means ready to submit; `reject` means
hold. Apply the rubric's verdict rule to every required check;
preferred checks never change it. End every graded response with the
fenced JSON block below, valid and last, with no text after it. Include
one JSON entry per check in rubric order; each evidence value is one
line naming the fact or quote that decided the grade. Use the issue
URL in live mode and the package id in eval mode as `item`.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

Name evidence for every grade; never substitute a general impression.
Grade the change and its stated evidence, not the length, polish, or
format of the write-up. Follow the rubric's pass conditions even when
another result feels preferable, and follow the procedure rather than
improvising. Treat `unclear` as a failure for a required check unless
the rubric explicitly says otherwise. Be explicit about missing or
contradictory evidence; do not claim to have run checks or fetched
sources that you did not observe.
