# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/104

**Branch**

`fix/18-github-file-structure`

## Eval iterations

**Run history**

Completed full runs, in order: **18/20 (PASS)**, **19/20 (PASS)**, **19/20 (PASS)**.
The first disagreed on `pkg-19` and `pkg-20`; the second disagreed on `pkg-04`; the
third disagreed on `pkg-05`. Focused `--only` regrades refined the rubric: `pkg-01`,
`pkg-04`, `pkg-05`, `pkg-07`, `pkg-19`, and `pkg-20` matched their gold labels on the
relevant revisions.

The current skill revision's confirming full run was attempted but stopped after five
packages completed; the remaining Claude Sonnet calls errored when the service reported
that the account had hit its spend limit. That partial run has no agreement score and
the harness did not save it.

**This is a progress copy, not the final eval submission.** A complete
current-fingerprint `eval-run.txt` is still pending. The starter `eval-run.txt` in this
directory remains the supplied placeholder; an earlier full run was not copied here
because it fingerprints an older skill revision.

**Package analysis**

`pkg-05`: the current focused regrade verdict was `accept`, matching the gold label
`accept`. The package shows the primary issue repro's before-and-after keybinding output,
then includes focused regression tests for same-name/different-key and same-key
replacement behavior plus the reported `cargo test -p nu-protocol` result. The rubric
requires observable proof of the primary behavior but allows a named regression test
and passing suite result to support its additional planned case; it does not demand a
separate log line for every test.

**Check rationale**

The `test-evidence` pass condition in the current `tools/pr-precheck/rubric.md` reads:

> Pass when evidence shows the primary issue behavior before and after under comparable repro conditions, through exact output or an explicit comparison such as `IDENTICAL`. If that repro cannot be run, a named, directly relevant regression test with its specific passing result may establish the post-fix behavior, but the reason the repro is unavailable must be stated. For additional planned cases, direct output or a regression test in the diff plus a reported passing result for the suite that runs it is sufficient; do not require each passing test's individual log line. A general claim such as "works now" or an unqualified suite-pass statement without observable primary behavior does not establish the fix. Also report relevant repository checks and actual outcomes. A failed or unavailable check may still pass this evidence check only when its status and reason are stated candidly; never describe it as passing. A material regression or a claim unsupported by evidence fails.

I revised this after `pkg-04` showed that “colors work now” plus an unqualified
`cargo test` claim was not observable proof of the reported behavior. `pkg-05` helped
set the boundary: its primary before/after output is concrete, while directly relevant
tests and the crate-suite result can support an additional planned case without
requiring each individual test log.

**Trade-offs**

The test-evidence check deliberately accepts a directly relevant regression test plus
the suite result for an additional planned case, as in `pkg-05`, but still rejects
`pkg-04`'s prose-only “colors work now” claim and generic suite-pass statement. I
re-graded `pkg-04` alongside `pkg-05` and `pkg-19` after setting that boundary; all
three then matched their gold labels. A current-revision full run is still needed to
confirm the final score and category floor after the Claude Sonnet usage limit blocked
the last attempt.
