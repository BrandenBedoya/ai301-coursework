# Rubric: is this a good first issue?

Seven required checks and three preferred ones. The required checks cover the
five surfaces the evidence guide names — maintainer alive, repo in use, scope
fits a newcomer, nobody else is on it, and the contribution policy allows
AI-assisted work. Every threshold below is measured against the bundle's
capture date in eval mode, and against today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-not-archived` | The `archived:` field on the `repo:` line of the repo-facts block. Live mode: the archived banner across the top of the repo front page. | `archived: no`. An archived repo is read-only, so no pull request can ever land; grade `fail`. | required |
| `commits-recent` | The dates in the `last 5 default-branch commits` list in the repo-facts block. Live mode: the newest commit date above the file list on the repo front page. | The newest of those five commit dates is within 120 days of the capture date. Bot-authored commits count only when the commit merges a human's pull request. | required |
| `unclaimed` | The `this issue: assignees:` and `linked PRs:` fields in the repo-facts block, plus every comment in the Comments section with its date. | All three hold: `assignees: none`; no linked PR is in the `open` state; and no comment claiming the work ("I'll take this", "can I work on this", "I've started to look at this", "working on this") is dated within 365 days of the capture date. A claim older than 365 days is stale and does not fail this check, and neither does a `closed` or `merged` linked PR. | required |
| `single-bounded-change` | The issue title and body, including any checklist or list of sub-items. | The issue asks for one deliverable that one pull request could finish. Grade `fail` when the text frames the work as ongoing or incremental, invites multiple independent pull requests, or is a tracking list of items that would each be their own change — signals such as "megaissue", "umbrella", "tracking issue", "incrementally adding more", "PRs are welcome both big and small", or a body that is mostly a list of other issue numbers. One deliverable broken into several steps in the same area (for example one docs page plus the existing pages that link to it) is bounded and passes. | required |
| `no-abandoned-attempts` | The issue's open date in the issue header compared with the capture date, and the state of each entry on the `linked PRs:` line. | Not both of: open for 365 days or more, **and** two or more linked pull requests in the `closed` state. A closed unmerged PR is an abandoned attempt; two of them on a year-old issue means the work is harder than it looks. `merged` PRs are not abandoned attempts and do not count. | required |
| `feature-has-maintainer-backing` | The issue's labels, the `author_association` of the opener in the issue header, and the `author_association` of each commenter. | This check applies only when the ask is **new user-facing behaviour** (a feature or enhancement). Grade `pass`, not `unclear`, for a bug report, a documentation task, or a test change — the check does not apply and is satisfied. When it does apply, pass only if a maintainer has endorsed it: the opener's association is OWNER, MEMBER or COLLABORATOR; or a comment from an OWNER, MEMBER or COLLABORATOR supports the approach; or the issue carries a `good first issue` or `help wanted` label. An unendorsed feature wish is a product decision nobody has made yet. | required |
| `ai-work-allowed` | The `contribution policy` line in the repo-facts block. Live mode: `CONTRIBUTING.md` in the repo root or `.github/`, the contributor docs it links to, any `AI_POLICY.md`, and the PR template. | The policy does not ban AI-assisted or AI-generated contributions. Conditions — disclose AI use, review and understand the output, test it yourself — are terms to follow, so they pass. Grade `pass`, not `unclear`, when no policy is stated: silence is not a restriction. Only an outright ban ("we do not accept AI-generated code or documentation") grades `fail`. | required |
| `released-recently` | The `latest release:` line in the repo-facts block. Live mode: the Releases box in the repo front page sidebar. | A release dated within 365 days of the capture date. `none published` grades `fail` on this preference only; because the weight is `preferred` it never rejects the issue, it only ranks it below repos that ship. | preferred |
| `maintainer-answers-issues` | The `maintainer first-response sample` list in the repo-facts block. Live mode: the first owner/member/collaborator reply on a few recently updated issues. | At least two of the five sampled issues drew a first maintainer comment within 30 days. | preferred |
| `newcomer-signposted` | The issue's labels and body. | The issue carries a `good first issue` or `help wanted` label, **or** the body names the specific file, function, page, or UI element to change. | preferred |

## Verdict rule

`accept` if every `required` check grades `pass`. A single `required` check
grading `fail` produces `reject`, and so does `unclear` on a required check:
a first issue whose evidence cannot be verified is not a first issue worth
taking.

`preferred` checks never change the verdict. They rank the issues that are
already accepted: prefer the candidate that passes more of them, using
`newcomer-signposted` first, then `maintainer-answers-issues`, then
`released-recently` as the tie-breaker. Report their grades either way.
