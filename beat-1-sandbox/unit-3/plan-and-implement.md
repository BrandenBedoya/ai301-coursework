# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

---

## Posted upstream

**GitHub username**

BrandenBedoya

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18#issuecomment-5922444095

Plan for this one, built on my reproduction above.

**Cause.** `GitHubTool._fetch_repo_metadata()` never fetches a file list, so the dict it returns has no `file_structure` key, and the analyzer's `.get("file_structure", "")` default makes every check false. My control run already showed the detectors are fine once the key exists:

```
failing:  has_tests = False  has_ci = False
control:  has_tests = True  has_ci = True
```

I also re-ran it through the real code (same machine, commit `f89c06f`), calling `_fetch_repo_metadata()` against this repo and passing the result to `RepoAnalyzer.parse()`: `file_structure present: False`, `has_tests = False  has_ci = False`.

**Change.** One change in `agent/tools/github_tool.py`: a new `_fetch_file_structure()` that calls the git trees API (`/git/trees/{default_branch}?recursive=1`) and returns every path as one newline-joined string, set as `file_structure`. I picked that shape because all three detectors do substring matching with `in str(...)`, so a flat string of full paths is what they already expect. It follows `_has_readme()`'s pattern, so a failed tree request returns `""` and the rest of the metadata still comes back. New tests go in `tests/unit/test_github_tool.py` with `httpx` mocked, including one that feeds the tool's output into `RepoAnalyzer` and asserts both flags are `True`.

**Not changing.** `RepoAnalyzer` or its heuristics. While reading I also noticed the two classes disagree on other key names (the analyzer reads `stargazers_count`, `language`, `pushed_at`, while the tool writes `star_count`, `primary_language`, `last_commit_date`), so stars and language come through as defaults too. That's a separate bug and I'm leaving it out of this change.

**Test.** Re-run my check through the real code. After the fix I expect `file_structure present: True` with a nonzero path count (214 for this repo right now) and `has_tests = True  has_ci = True`. Then the new unit tests, and `pytest tests/unit` with no new failures.

**Open questions.** The trees API truncates very large repos, and I plan to keep the partial list and log a warning rather than paginate. I'll call that out in the PR in case a different trade-off is preferred. I haven't tested against an empty repository yet.

I read ColonelToad's plan above, which uses the same tree endpoint. This one is mine, worked out from my own reproduction.

For transparency: I work with AI assistance, and I run and verify everything before I post it.

---

## Your branch

**Branch**

`fix/18-github-file-structure`

**Evidence**

My Unit 2 repro (`repro_18.py`) feeds `RepoAnalyzer` a hand-built copy of the dict
`GitHubTool` returns, so it is a stand-in: it never calls `GitHubTool` and cannot show the
fix. As the assignment allows, I turned its inputs into a check that runs through the real
code. `check_18.py` calls the real `GitHubTool._fetch_repo_metadata("codepath",
"pathreview-ai301-fa26-s1")` against the live GitHub API and passes the result straight to
`RepoAnalyzer.parse()`, the same handoff `ingestion/pipeline.py:239` makes:

```python
import sys

sys.path.insert(0, ".")
from agent.tools.github_tool import GitHubTool  # noqa: E402
from ingestion.parsers.repo_analyzer import RepoAnalyzer  # noqa: E402

repo_data = GitHubTool()._fetch_repo_metadata("codepath", "pathreview-ai301-fa26-s1")
fs = repo_data.get("file_structure")
print("file_structure present:", "file_structure" in repo_data)
print("file_structure paths:  ", len(fs.splitlines()) if fs else 0)
m = RepoAnalyzer().parse(repo_data).metadata
print("has_tests =", m["has_tests"], " has_ci =", m["has_ci"])
```

Before, on `main` at `f89c06f` (macOS 15.7.7 arm64, Python 3.11.3):

```
$ git log --oneline -1
f89c06f chore: track five more manifest entries against the tracker
$ python3 repro_18.py
failing:  has_tests = False  has_ci = False
control:  has_tests = True  has_ci = True
$ .venv/bin/python check_18.py
file_structure present: False
file_structure paths:   0
has_tests = False  has_ci = False
```

After, on `fix/18-github-file-structure` (the change is committed as `1d8e76e`; at capture
time it was the working-tree diff on top of `f89c06f`, shown by `git diff --stat`):

```
$ git log --oneline -1 && git diff --stat
f89c06f chore: track five more manifest entries against the tracker
 agent/tools/github_tool.py | 40 ++++++++++++++++++++++++++++++++++++++++
 1 file changed, 40 insertions(+)
$ python3 repro_18.py
failing:  has_tests = False  has_ci = False
control:  has_tests = True  has_ci = True
$ .venv/bin/python check_18.py
file_structure present: True
file_structure paths:   214
has_tests = True  has_ci = True
$ .venv/bin/python -m pytest tests/unit/test_github_tool.py -v -m unit
tests/unit/test_github_tool.py::TestGitHubTool::test_file_structure_lists_every_tree_path PASSED [ 33%]
tests/unit/test_github_tool.py::TestGitHubTool::test_failed_tree_request_returns_empty_file_structure PASSED [ 66%]
tests/unit/test_github_tool.py::TestGitHubTool::test_repo_analyzer_detects_tests_and_ci_from_tool_output PASSED [100%]
============================== 3 passed in 0.03s ===============================
```

`repro_18.py` prints the same two lines before and after, which is expected: it only
exercises the analyzer, and the plan leaves the analyzer untouched. The real-code check
flips from `False False` to `True True`, with the key present and 214 paths.

The new regression tests, run against `main`'s `github_tool.py` (copied into a clean
worktree at `f89c06f`), fail all three:

```
$ pytest tests/unit/test_github_tool.py -q -m unit
E       assert False is True
tests/unit/test_github_tool.py:92: AssertionError
FAILED tests/unit/test_github_tool.py::TestGitHubTool::test_file_structure_lists_every_tree_path
FAILED tests/unit/test_github_tool.py::TestGitHubTool::test_failed_tree_request_returns_empty_file_structure
FAILED tests/unit/test_github_tool.py::TestGitHubTool::test_repo_analyzer_detects_tests_and_ci_from_tool_output
3 failed in 0.14s
```

and on the branch, all three pass (above). The full unit suite on the branch:
`378 passed, 53 xfailed`, with `ruff check .` and the CI mypy command both clean.

## Eval iterations

**Run history**

One run:

1. Full run, all 20 scored packages, saved with `--save-run`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with
   `categories: clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.

That is the run in the committed `eval-run.txt`. I did not revise anything after it, so the
fingerprints in its header (`rubric.md sha256:02b744a39a91b1bf`,
`evidence-guide.md sha256:c54d0eb25498534c`, `procedure.md sha256:152a88f1989ac581`) still
match the files uploaded to `tools/plan-check/`.

It took one run because I spent the effort before paying for any. I read all 20 scored
packages and the 4 calibration packages first, and wrote down, for each one, which part of
it should decide the verdict. Then I checked that every gold reject failed at least one
required check and that no gold accept tripped any. Where a check was close, I wrote the
edge case into its pass condition (sibling-site fixes and audit-and-report stay in scope;
an unknown about the exact line inside a named area still counts as buildable). I also
checked that the 20/20 was for the right reasons and not luck: each wrong-cause package
failed `diagnosis-fits-evidence` on the specific control that rules its diagnosis out
(`pkg-16` on step 4, where the zeros are already gone before the cast), and `pkg-04`
failed `follows-thread-direction` on the owner's patched binary.

**Package analysis**

`pkg-09` (sharkdp/fd#2067). My rubric decided **accept**, and the gold label is **accept**.
I chose it because it is the package my Unit 2 rubric would have gotten wrong. The repo's
policy says:

> AI-assisted code contributions are accepted from contributors who understand the code
> and must state the tool and the extent of its use in the pull request; comments to
> maintainers are expected to be in the contributor's own words and voice (AI help with
> grammar, spelling, and proofreading is fine); the policy states no disclosure ask for
> issue comments

and the candidate plan comment contains no AI disclosure at all. My Unit 2
`conventions-respected` check read a "comments in your own words" rule as a disclosure
requirement, which is how it falsely rejected Unit 2's `pkg-03`. Read that way, this
package would fail too.

This rubric splits the policy into two rules. The disclosure ask applies to the pull
request, not the comment. "Own words" is an authorship rule, and the comment does not say
it was AI-written. The run's evidence line for `ai-disclosure-where-required` reads:

> Policy: 'no disclosure ask for issue comments'; authorship-only rule satisfied since
> comment doesn't claim AI authorship.

Everything else held up too. `follows-thread-direction` passed because the comment names
the collaborator's option 2 and engages PR #2089 instead of racing it ("I have read PR
#2089, which takes the same route"). `one-bounded-change` passed because option 1 (direct
globset matching) is explicitly deferred. The gold note calls it "arguable on the
deferral", and my rubric agrees with the gold side of that because deferring named work
does not count against scope.

**Check rationale**

| `ai-disclosure-where-required` | The repo-facts block's contribution-policy line, read against the text of the plan comment. Every package is treated as AI-assisted work. | Grade `fail` only when the policy requires AI use to be disclosed in issues or comments, or requires disclosure of all AI usage in any form, **and** the plan comment contains no disclosure. Grade `pass` when the policy is silent on AI, permits AI with responsibility or review rules, or asks for disclosure only in pull requests. A policy requiring comments to be written by a human in their own words is an authorship rule, not a disclosure rule: it grades `pass` unless the comment itself says it was AI-written. | required |

It reads this way because of Unit 2. There, `conventions-respected` folded every
repo-convention question into one check. It conflated "comments must be written by humans"
with "you must disclose AI use", and produced a false reject on `pkg-03`. I could not fix
it there without risking `pkg-20`, the only package holding up Unit 2's `disclosure`
category.

This unit starts fresh, so I built the distinction in from the beginning, using the
policy lines across all 20 packages. Exactly one, `pkg-20` (ghostty), requires disclosure
in comments ("All AI usage in any form must be disclosed"). Two others, `pkg-03` (ripgrep)
and `pkg-09` (fd), have human-authorship rules and are gold accepts. Two more, `pkg-05`
and `pkg-07`, have "review and understand AI content" rules with no disclosure ask. So the
check fails only on an actual disclosure requirement, and it names the authorship rule as
a separate thing that passes. I rejected the simpler wording, "satisfies the repo's
stated conventions", because that is the wording that failed in Unit 2.

In the run this check is load-bearing. `pkg-20` failed it and nothing else, since every
other required check passed on what the gold note calls an "excellent bounded plan". Without
this check, `pkg-20` flips to accept and `thread-convention` drops to 1/2.

**Trade-offs**

The check gives up any ability to catch an authorship violation. Under a ripgrep- or
fd-style policy, a comment that obviously reads as machine-generated still grades `pass`,
because the check only fails when the comment *says* it was AI-written. I accept that
miss on purpose. "Does this read as AI-written" is a judgment two graders would not agree
on, and the human-voice question is already handled per author by the live-mode voice
guide, which never sets the verdict.

Nothing else in the run changed because of it, and I can show why. In the saved results,
`ai-disclosure-where-required` graded `pass` on 19 of 20 packages and `fail` only on
`pkg-20`. So it decides exactly one verdict, the one it was written for. The other 19 are
decided by the other five required checks.

The risk is in tightening it later. If I made the authorship rule count as a failure,
`pkg-09` (no disclosure, own-words rule, gold accept) would flip to reject. I would add
`pkg-09` and `pkg-03` as canaries on any `--only` re-run, along with `pkg-20` to make sure
the disclosure case still fails. I did not re-run anything with `--only`, because the
first full run agreed on all 20 and I did not edit the files afterwards.

Two gaps showed up in live mode that the eval cannot see, and I am leaving them as known:
`procedure.md` reads the policy from "the repo-facts block", which only exists in eval
mode, and no check grades the plan's `## Deviations` section. Fixing either changes a
fingerprinted file and means paying for another full run.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
