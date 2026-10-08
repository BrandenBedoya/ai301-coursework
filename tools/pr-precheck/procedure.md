# Procedure: how this tool grades a PR package

## Read order

1. Live mode only: read `scope.md` first. Refuse if the repo is a
   placeholder or the issue URL is outside the scoped repo; otherwise
   record the house rules. Eval mode skips scope and all external
   sources.
2. Read `rubric.md` and `references/evidence-guide.md`. Record the
   four required checks in their listed order and the verdict rule.
3. Read the issue and its thread highlights (or the frozen eval
   bundle's Issue and Thread highlights). Record the reported behavior
   and any direction from a maintainer or other repository authority.
4. Read repo-facts' template asks and contribution/AI policy. In live
   mode, use `.github/PULL_REQUEST_TEMPLATE.md`,
   `docs/CONTRIBUTING.md`, and any policy they identify. Record each
   explicit requirement and whether it can be verified before opening
   the PR; do not treat post-open CI as available.
5. Read the accepted plan, including its test plan and any
   `## Deviations`, before reading the diff. Record the planned change,
   boundaries, files, expected behavior, and named tests.
6. Read the full diff and commit list. Then read the PR title and
   description, followed by all test evidence. This order makes the
   claims testable against observed changes and outputs instead of
   letting the description define what the diff is supposed to show.
7. In eval mode, treat the package as complete and stop gathering:
   do not read its JSON twin, fetch anything, or inspect other files.
   In live mode, use only the named working-copy files, diff, issue,
   and repo standards.

## Evidence gathering

1. For `plan-fidelity`, list each distinct proposed change and each
   material diff hunk. Pair them: mark as implemented, regression
   test, documented deviation, deferred/out of scope, or unexplained.
   Compare every material description claim with the corresponding
   diff hunk. Record any promised omission or unclaimed addition.
2. For `test-evidence`, pair each plan test and reproduced behavior
   with its before/after output or regression-test result. List each
   repo check named by the repo instructions or assignment and its
   recorded outcome. Record missing output, unexplained failures, and
   claims of success that the output does not support. A check that
   has not run is not a passing check.
3. For `reviewable-diff`, walk all changed files and hunks, including
   tests and commits. Mark any change with no clear link to the issue,
   plan, test, or documented deviation; separately note debug output,
   dead/commented-out code, or broad mechanical churn.
4. For `repo-standards`, make a checklist from each applicable
   template/policy ask. Match it to the title, issue reference,
   description section, diff, or test evidence that satisfies it.
   Check the thread for a direction relevant to this change and locate
   the plan/description's response. Record required disclosure
   separately from authorship rules. Treat a course eval package and
   the live course PR as AI-assisted unless it explicitly says
   otherwise; do not infer "no AI use" from a missing disclosure. For
   a policy requiring tool and extent, verify both are stated. Mark
   post-open CI as not yet available, not as a failure to provide
   pre-PR evidence.
5. When an artifact or fact is genuinely absent, record exactly what
   is missing. Do not infer test output, policy, issue facts, or a
   deviation from silence.

## Check execution

1. Grade the checks in rubric order, using only the paired evidence
   gathered above and the exact pass condition in `rubric.md`.
2. `plan-fidelity`: fail for any material unexplained addition,
   deviation not recorded in the plan, promised work absent without
   an explained deviation, or material description claim contradicted
   by the diff. Do not fail work explicitly deferred or out of scope.
3. `test-evidence`: fail if the primary issue behavior lacks observable
   before-and-after evidence under comparable repro conditions, the
   expected post-fix behavior is not demonstrated, a required relevant
   check has no reported outcome, a failure is concealed/misrepresented,
   or results contradict the claims. Exact output or an explicit
   comparison (such as `IDENTICAL`) demonstrates the primary behavior.
   If its repro could not be run, require the reason and a named,
   directly relevant regression test with its specific passing result.
   For additional planned cases, accept direct output or a regression
   test in the diff plus a reported passing result for the suite that
   runs it; an individual test log line is not required. A generic
   suite-pass claim or "works now" alone cannot establish the primary
   behavior. Do not require duplicate proof of the same behavior. An
   honestly reported failure with a specific reason can pass this
   evidence-presence check when it does not contradict the stated
   expected behavior.
4. `reviewable-diff`: fail if unrelated work or debris makes the
   intended fix harder to isolate or review. Do not treat a
   documented, issue-related change or a regression test as debris.
5. `repo-standards`: fail if any applicable explicit ask is missing
   or contradicted, required disclosure is absent, or relevant
   maintainer direction is ignored. For eval and live course work,
   treat the package as AI-assisted unless it explicitly says it was
   not; missing disclosure does not establish non-use. If a policy is
   silent, do not invent an eval disclosure requirement; in live mode,
   still require the course disclosure in Notes for Reviewers. If no
   maintainer direction exists, that part passes.
6. Use `unclear` only when the needed evidence cannot be established
   from the allowed sources, rather than as a softer judgment of weak
   evidence. A genuinely absent required artifact may be `unclear`;
   evidence that is present but fails a condition is `fail`.
7. Grade every check even after one fails. Each evidence field in the
   output must be a concise factual quote or observation, not a
   summary like "not ready".

## Verdict assembly

1. Apply the verdict rule in `rubric.md`: accept only when all four
   required checks pass; a required fail or unclear means reject.
2. In the readable summary, show every check and its grade. For a
   reject, identify the first failing or unclear required check in
   rubric order and quote the specific evidence that decided it.
3. In live mode, report voice-guide violations before the JSON block;
   do not use them to change the verdict unless independently covered
   by a rubric check.
4. Emit the exact JSON schema in `SKILL.md` as the final content.
   Include one check object per rubric row in rubric order, valid JSON,
   one-line evidence values, and no text after the closing fence.
