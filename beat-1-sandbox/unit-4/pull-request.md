# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/104

**Branch**

`fix/18-github-file-structure`

## Eval iterations

**Run history**

Full runs, in order:

1. **18/20 (PASS)**: disagreed on `pkg-19` and `pkg-20`.
2. **19/20 (PASS)**: disagreed on `pkg-04`.
3. **19/20 (PASS)**: disagreed on `pkg-05`.
4. **No score**: a confirming run on the revised rubric stopped after five packages when
   the account hit its Claude spend limit. The harness marked the other 15 as errors and
   refused to save it.
5. **19/20 (PASS)**: the run in `eval-run.txt`, on the current files
   (`rubric.md sha256:a42acad82d4894fc`). Categories: `clear-accept 6/7  not-tested 4/4
   silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. Disagreed on `pkg-02`.

Between full runs, focused `--only` regrades checked each revision: `pkg-01`, `pkg-04`,
`pkg-05`, `pkg-07`, `pkg-19`, and `pkg-20` each matched gold on the revision meant to fix
them, with `pkg-01` and `pkg-20` as standards-wall canaries.

**Package analysis**

`pkg-02` (Rich, `Console.save_*` loses recorded output when the write fails): my rubric
said `reject`, the gold label is `accept`. Every other check passed. `test-evidence`
failed with the note that `save_html` and `save_svg` "got the identical fix with zero
output or regression test covering their new behavior, only a pre-existing full-suite
pass."

The gold label is right and my rubric's run was wrong. The plan's test plan names three
things: re-run the issue's script and see `'IMPORTANT OUTPUT\n'` survive the failed save,
confirm a successful save still clears, and run pytest. The evidence shows all three:
the exact before (`''`) and after (`'IMPORTANT OUTPUT\n'`) output, the new test covering
the successful-save clear, and `1462 passed`. The grader asked for proof the plan never
promised. My pass condition says "for additional planned cases", so `save_html` and
`save_svg` were never in the bar, but nothing in the rubric says outright that unplanned
cases are not required, and Sonnet filled that gap with its own standard.

**Check rationale**

The `test-evidence` pass condition in `tools/pr-precheck/rubric.md` reads:

> Pass when evidence shows the primary issue behavior before and after under comparable repro conditions, through exact output or an explicit comparison such as `IDENTICAL`. If that repro cannot be run, a named, directly relevant regression test with its specific passing result may establish the post-fix behavior, but the reason the repro is unavailable must be stated. For additional planned cases, direct output or a regression test in the diff plus a reported passing result for the suite that runs it is sufficient; do not require each passing test's individual log line. A general claim such as "works now" or an unqualified suite-pass statement without observable primary behavior does not establish the fix. Also report relevant repository checks and actual outcomes. A failed or unavailable check may still pass this evidence check only when its status and reason are stated candidly; never describe it as passing. A material regression or a claim unsupported by evidence fails.

It reads this way because of two packages pulling in opposite directions. `pkg-04`
claimed "colors work now" plus an unqualified `cargo test` pass, which earlier rubric
versions accepted, so the check now demands observable before-and-after output for the
primary behavior and says a generic suite pass is not enough. That tightening then failed
`pkg-05`, a gold accept with a concrete primary repro whose extra case was covered by a
named test and a crate-suite result, so I added the sentence that a regression test plus
its suite result is enough for additional planned cases, without a log line per test.

**Trade-offs**

`pkg-04`, `pkg-05`, and `pkg-02` all sit on the same line: how much proof beyond the
primary repro a PR owes. Tightening the check fixed `pkg-04`; loosening it for planned
extra cases fixed `pkg-05`; and in the final run the grader still read `pkg-02` strictly
and rejected it. The fix is one more clause ("cases the plan's test plan does not name
are not required"), but each full run costs about $5 and the change loosens the check
holding up the `not-tested` category, so it could flip `pkg-04` or `pkg-07` back. With
19/20 and every category matched, I kept the stricter reading. A tool that sometimes asks
for an extra test on a ready PR costs a few minutes; one that waves through an untested
PR is what the check exists to stop.
