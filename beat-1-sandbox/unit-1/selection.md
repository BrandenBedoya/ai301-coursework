# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18

**Verdict output**

Live-mode run of the installed `issue-select` skill on two candidates. The skill
accepted both and ranked #18 first on the fit profile in `scope.md`.

````
## Summary

Both issues are in-scope (`codepath/pathreview-ai301-fa26-s1`), and both clear every required check — repo is active (newest commit 11 days old, not archived), each is a single bounded fix with named files, neither is genuinely claimed (classmate claim comments don't count per the course house rule, and the only linked PR anywhere is a **merged** one in an unrelated fork, not an open claim), the opener (Aburke225) is a COLLABORATOR so maintainer-backing is satisfied either way, and `docs/CONTRIBUTING.md` states no AI-use ban (silence passes).

**Ranked (both accepted):**

1. **#18 — Repo analyzer never receives a file list, so `has_tests`/`has_ci` are always `False`** — Best fit for you: this *is* the tool→consumer data-contract seam you said you want to build muscle on (`GitHubTool` never writes the `file_structure` key that `repo_analyzer.py` reads), in the agent/ingestion subsystems you ranked higher. Preferred checks: `good first issue` label + named files (pass), no releases (fail), no maintainer replies in the 5-issue sample (fail).
2. **#69 — Output parser crashes on a top-level JSON array fallback** — Also strong: RAG-subsystem parsing/edge-case bug, and a classmate already posted a full local reproduction + traceback you could reuse to verify your own fix. Ranks second only because it's a generic missing-`isinstance`-check rather than the specific producer/consumer contract mismatch you named. Same preferred-check grades as #18 (repo-level checks are identical since same repo).

Rejected: none.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "newest default-branch commit 2026-09-16, 11 days before today (2026-09-27)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; no linked PR at all; ColonelToad's claim comment doesn't block per the Path Review classmate house rule"},
      {"name": "single-bounded-change", "grade": "pass", "evidence": "one deliverable — wire file_structure through agent/tools/github_tool.py into ingestion/parsers/repo_analyzer.py"},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "opened 2026-09-10, only 17 days old, well under the 365-day threshold"},
      {"name": "feature-has-maintainer-backing", "grade": "pass", "evidence": "opener Aburke225 has author_association COLLABORATOR"},
      {"name": "ai-work-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no AI-use ban; only workflow/CI conditions"}
    ],
    "verdict": "accept",
    "preferred": [
      {"name": "released-recently", "grade": "fail", "evidence": "0 releases published in the repo"},
      {"name": "maintainer-answers-issues", "grade": "fail", "evidence": "0 of 5 sampled recently-updated issues (#35,#53,#72,#70,#51) drew any OWNER/MEMBER/COLLABORATOR comment"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "carries 'good first issue' label and names github_tool.py / repo_analyzer.py"}
    ]
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69",
    "checks": [
      {"name": "repo-not-archived", "grade": "pass", "evidence": "isArchived: false"},
      {"name": "commits-recent", "grade": "pass", "evidence": "newest default-branch commit 2026-09-16, 11 days before today (2026-09-27)"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; only cross-referenced PR (charancherry0/ai301-coursework#1) is merged, not open; jacho15's claim comments don't block per the classmate house rule"},
      {"name": "single-bounded-change", "grade": "pass", "evidence": "one deliverable — guard parse_review_output with isinstance(data, dict) and drop the H-02 xfail marker"},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "opened 2026-09-10, only 17 days old, well under the 365-day threshold"},
      {"name": "feature-has-maintainer-backing", "grade": "pass", "evidence": "labeled 'bug', so the check doesn't apply (auto-pass); opener is also COLLABORATOR"},
      {"name": "ai-work-allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no AI-use ban; only workflow/CI conditions"}
    ],
    "verdict": "accept",
    "preferred": [
      {"name": "released-recently", "grade": "fail", "evidence": "0 releases published in the repo"},
      {"name": "maintainer-answers-issues", "grade": "fail", "evidence": "0 of 5 sampled recently-updated issues (#35,#53,#72,#70,#51) drew any OWNER/MEMBER/COLLABORATOR comment"},
      {"name": "newcomer-signposted", "grade": "pass", "evidence": "carries 'good first issue' label and names rag/generator/output_parser.py + the test file"}
    ]
  }
]
```
````

---

## Eval iterations

**Run history**

Two runs, in order:

1. Smoke run, `--limit 3` (issue-01, issue-02, issue-03) — `agreement: 3/3 scored items`.
   Partial run, so no bar verdict and no saved transcript; I used it only to confirm the
   skill parsed my rubric and emitted a valid JSON block before paying for a full run.
2. Full run, all 20 scored bundles, saved with `--save-run` —
   `agreement: 19/20 scored items  (bar: 18/20: PASS)`, with
   `categories: claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 4/4`.

The second score is the one in the committed `eval-run.txt`. I did not revise the rubric
after that run: `eval-run.txt` fingerprints the rubric it graded
(`rubric.md  sha256:91d1ce3f34be2066`), and that digest still matches the `rubric.md`
uploaded to `tools/issue-select/`, so the quoted check below is the check that produced
the run.

**Issue analysis**

`issue-19` (zxcalc/zxlive#517, "Selecting large subgraphs in proof mode freezes the UI").
My rubric returned **reject**; the gold label is **accept**. It was the only disagreement
in the run.

Every required check passed except `single-bounded-change`, which failed with this
evidence line from the run:

> Body lists two causes to fix plus an 'Additional suggestions' list of three further
> independent optimizations (multiprocessing, category-scoped matching, separate
> rewrite-application thread)

The bundle body is a numbered list of two causes followed by three more suggestions, so
my check counted five separately-actionable items and read the issue as a container for
several changes rather than one. The gold note reads it the other way — "maintainer-
diagnosed performance bug with named causes" — treating the freeze as a single bug that a
COLLABORATOR had already diagnosed, with the suggestions as optional follow-on work rather
than required deliverables.

The disagreement is not a missed signal. It is a threshold question my check answers with
a count of list items when the distinction that actually matters is whether the listed
items are required to close the issue. On this issue that distinction is genuinely
arguable, which is why I left the check as written rather than tuning it to this one
bundle.

**Check rationale**

The check that produced the disagreement, quoted as it is currently written in
`tools/issue-select/rubric.md`:

> | `single-bounded-change` | The issue title and body, including any checklist or list of
> sub-items. | The issue asks for one deliverable that one pull request could finish. Grade
> `fail` when the text frames the work as ongoing or incremental, invites multiple
> independent pull requests, or is a tracking list of items that would each be their own
> change — signals such as "megaissue", "umbrella", "tracking issue", "incrementally adding
> more", "PRs are welcome both big and small", or a body that is mostly a list of other
> issue numbers. One deliverable broken into several steps in the same area (for example one
> docs page plus the existing pages that link to it) is bounded and passes. | required |

Two decisions are doing the work. First, it keys on how the issue *frames* the work rather
than on how long the body is, because the eval set punishes a length heuristic in both
directions: `issue-01` is a gold accept whose body specifies a new docs page plus edits to
three named existing pages, while `issue-20` is a gold reject that is four short
paragraphs. Second, the closing sentence is an explicit carve-out I added after tracing
`issue-01` by hand — without it, that issue's five-item list of pages to update reads as a
tracking list and the check wrongly rejects a clear accept. The named signals
("megaissue", "umbrella", "incrementally") come from the actual wording of `issue-10`
("Documentation request megaissue") and `issue-05` ("this issue is about incrementally
adding more type annotations... PRs are welcome both big and small").

**Trade-offs**

The check buys both `scope`-category rejects at the cost of `issue-19`, and that is the
whole trade.

The carve-out is written around a *docs* shape — one artifact plus the pages that point at
it. `issue-19` is the shape it does not cover: one bug with several named causes, where
the list is a maintainer's diagnosis rather than a work breakdown. Widening the carve-out
to pass `issue-19` would mean ignoring lists whose items are optional, and I could not
write that condition in a way a second person would apply the same way — "optional" is not
visible in the bundle text, which is exactly the adjective-over-threshold problem the
rubric is supposed to avoid.

So I accept the miss. The category floor still holds (`scope 4/4`), the run is 19/20
against a bar of 18, and the issue it misses is one the assignment itself flags as an
arguable scope call. The alternative — loosening the check until `issue-19` passes — risks
`issue-05` and `issue-10`, which are unambiguous rejects, to recover one arguable one.

---

## Selection rationale

**Selection rationale**

<!-- BRANDEN: this section is graded on being in YOUR OWN WORDS. Answer all three below,
     then delete these comment lines. Short and honest is explicitly enough. -->

1. *The issue's fit to your interests and to the time available.*

   TODO

2. *What the verdict identified correctly, and what you weighed that the rubric could not.*

   TODO

3. *The anticipated difficulty in claiming it.*

   TODO
