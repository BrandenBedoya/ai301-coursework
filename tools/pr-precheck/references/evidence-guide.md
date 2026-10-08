# Evidence guide: where evidence lives in a PR package

The map for every check in `rubric.md`. Each family says where to
look in an eval bundle, where to look live, and what good looks like.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** Bundle: Plan context's scope, proposed changes,
files, approach, test plan, and deviation notes, compared with
Candidate PR's diff and description. Live: `plan.md` including
`## Deviations`, `git diff main...HEAD`, and `pr_draft.md`.

**What good looks like.** Every material change is inside the plan's
boundary or is explained in a deviation; promised work is present or
its omission is explained. The description accurately says what the
diff does. Silence about a mismatch is drift; explicit deferral is not
a promise to implement.

## Test evidence (harness category: not-tested)

**Where it lives.** Bundle: Plan context's repro and test plan beside
Candidate PR's test-evidence section and regression-test diff. Live:
the same test plan in `plan.md`, the issue's reproduction evidence,
`test_evidence.md`, and test-related diff hunks.

**What good looks like.** The primary issue repro shows the failure
before and the expected behavior after under comparable conditions.
Exact output or an explicit comparison such as `IDENTICAL` is
observable. If that repro cannot be run, its unavailability is
explained and a named, directly relevant regression test has a
specific passing result. Additional planned cases may be shown by
output, or by tests in the diff plus a reported passing result from
the suite that runs them; individual test log lines are not required.
A prose claim like "works now" or an unqualified suite pass alone
does not prove the primary behavior. Repository checks have actual
outcomes, including candid explanations for failed or unavailable
checks.

## Diff quality (harness category: unreviewable)

**Where it lives.** Bundle: Candidate PR's full unified diff and
commit list. Live: `git diff main...HEAD` and the commits on the
current branch.

**What good looks like.** Each changed hunk supports the issue's fix,
a regression test, or a recorded deviation. Debug output, dead or
commented-out code, unrelated edits, and broad formatting churn are
debris when they obscure or broaden the intended change. A test file
or a commit is not debris merely because it is separate.

## Standards and comms (harness category: standards-wall)

**Where it lives.** Bundle: Repo facts' PR-template asks and
contribution/AI policy, Issue thread highlights, Candidate PR title
and description, diff, and test evidence. Live:
`.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, any
identified AI policy, the issue thread, `pr_draft.md`, and evidence
in `test_evidence.md`.

**What good looks like.** Each applicable explicit repo ask has
substantive matching content or evidence; required issue references
identify the correct issue; policy-required AI disclosure is present;
and relevant maintainer direction is acknowledged or followed. A
template section filled only with placeholder text is not complete.
Treat eval and live course PRs as AI-assisted unless the package
explicitly says otherwise; do not infer no AI use from silence. When
policy requires tool and extent, both are disclosed. Do not demand
disclosure from an AI-silent policy in eval, and do not claim
post-open CI has run before it exists. Live course PRs still require
the course disclosure under Notes for Reviewers.
