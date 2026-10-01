# Plan: PathReview #18, repo analyzer never receives a file list

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18
Repro I am building on: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18#issuecomment-5862797914
Branch: `fix/18-github-file-structure` on my fork, from `main` at `f89c06f`.

## Diagnosis

`GitHubTool._fetch_repo_metadata()` (`agent/tools/github_tool.py`) never fetches the
repository's file list and never sets a `file_structure` key. `RepoAnalyzer`
(`ingestion/parsers/repo_analyzer.py`) reads that key in `_detect_ci()`,
`_detect_tests()`, and `_detect_tech_stack()` through `.get("file_structure", "")`, so
the default empty string applies and every substring test comes back false. The
detectors themselves are correct; they are never given anything to read.

The repro evidence this rests on, from my posted report. The failing run used the exact
ten-key dict `_fetch_repo_metadata()` builds, and the control is the same dict with only
`file_structure` added by hand:

```
failing:  has_tests = False  has_ci = False
control:  has_tests = True  has_ci = True
```

and the key list `_fetch_repo_metadata()` returns:

```
['name', 'description', 'primary_language', 'star_count', 'fork_count',
 'open_issues_count', 'last_commit_date', 'has_readme', 'topics', 'homepage']
```

The control rules out the detectors as the cause (same analyzer, flags flip to `True`
the moment the key exists), and the key list shows the producer never writes it. So the
fix belongs in the producer, not the analyzer.

I also re-ran this through the real code before planning, calling the live
`GitHubTool._fetch_repo_metadata("codepath", "pathreview-ai301-fa26-s1")` and passing its
output straight to `RepoAnalyzer.parse()`, the same handoff `ingestion/pipeline.py:239`
makes:

```
file_structure present: False
file_structure paths:   0
has_tests = False  has_ci = False
```

## Scope

**In scope, one change:** `GitHubTool` fetches the repository's file paths and returns
them under `file_structure`, plus unit tests for that.

**Not in scope:**

- Any change to `RepoAnalyzer` or its detection heuristics. The control run shows they
  already work on the right input.
- The other key-name mismatches between the two classes. While reading, I noticed the
  analyzer reads `stargazers_count`, `forks_count`, `language`, `readme_content`,
  `pushed_at`, and `html_url`, while `GitHubTool` writes `star_count`, `fork_count`,
  `primary_language`, `has_readme`, and `last_commit_date`. That is a separate bug with
  its own symptoms (stars, forks, and language read as defaults). I will mention it on
  the thread, not fix it here.
- Pagination, caching, or rate-limit handling beyond what `_has_readme()` already does.

## Files

- `agent/tools/github_tool.py`: new private method `_fetch_file_structure()`, one new
  key in the dict `_fetch_repo_metadata()` returns.
- `tests/unit/test_github_tool.py`: new file (there is no `GitHubTool` test file today).

## Approach

1. **Shape of `file_structure`: a newline-joined string of every path in the repo**,
   for example `".github/workflows/ci.yml\ntests/unit/test_x.py\npyproject.toml"`. All
   three detectors do `"<needle>" in str(file_structure)`, so a flat string of full paths
   is what they already expect, and it is the same shape my repro's control used. A
   nested structure would make `str()` produce dict syntax, and a list would produce
   Python list syntax, both of which happen to work for substrings but by accident.
2. **Where the paths come from:** GitHub's git trees API,
   `GET /repos/{owner}/{repo}/git/trees/{default_branch}?recursive=1`, using
   `repo_json["default_branch"]` from the response `_fetch_repo_metadata()` already has.
   I checked this endpoint against PathReview itself: 214 entries, `truncated: false`,
   including `.github/workflows/ci.yml` and `tests/unit/test_readme_parser.py`. I will
   include both `blob` and `tree` entries so a directory path like `.github/workflows`
   is present even if it held no files.
3. **`_fetch_file_structure(username, repo_name, branch) -> str`** mirrors
   `_has_readme()`: same auth header handling, same `try/except` that returns a safe
   default (`""`) instead of raising. A failed tree call then degrades to today's
   behavior rather than failing the whole metadata fetch, which already succeeded.
4. If the API reports `truncated: true`, keep the partial list and log a warning with
   `structlog`, matching the file's existing logging style.
5. Google-style docstring on the new method, per `docs/CONTRIBUTING.md`.

## Test plan

1. **Unit 2 repro re-run.** `python3 repro_18.py` exercises only the analyzer with a
   hand-built dict, so it should print the same two lines before and after
   (`failing: ... False False`, `control: ... True True`). That confirms the analyzer was
   left alone. It cannot show the fix, since it never calls `GitHubTool`.
2. **The same inputs through the real code.** `check_18.py` calls the real
   `GitHubTool._fetch_repo_metadata("codepath", "pathreview-ai301-fa26-s1")` and passes
   the output to `RepoAnalyzer.parse()`. Before (recorded above): `file_structure
   present: False`, `has_tests = False  has_ci = False`. **Expected after:**
   `file_structure present: True`, a path count above 0 (214 at the time of writing),
   and `has_tests = True  has_ci = True`.
3. **New unit tests** in `tests/unit/test_github_tool.py`, with `httpx.get` and
   `httpx.head` monkeypatched so no network is used:
   - the tree response's paths come back newline-joined under `file_structure`;
   - a failing tree request gives `file_structure == ""` and the other metadata keys are
     still returned;
   - end to end: the patched `GitHubTool` output passed to `RepoAnalyzer.parse()` gives
     `has_tests is True` and `has_ci is True`. This is the regression test for the issue
     itself; it fails on `main`.
4. `pytest tests/unit` passes with no new failures, and `ruff check` and `mypy` are clean
   on the two touched files.

## Risks and unknowns

- **Truncation on very large repos.** The trees API truncates past about 100,000
  entries. I am keeping the partial list and logging, not paginating. That is a
  judgment call, and I will flag it in the PR.
- **Rate limits.** This adds a third unauthenticated request per repo (60 per hour
  limit without a token). `_has_readme()` already makes a second one, so I am
  following the existing pattern rather than solving it here.
- **Empty repositories.** A repo with no commits has no tree, and the trees endpoint
  returns an error. The `try/except` should turn that into `""`, which I will cover in
  the failing-request test, but I have not run it against a real empty repo.
- **Substring false positives.** A path such as `contest/` contains `test/`. That is
  the analyzer's existing heuristic and out of scope; I note it so nobody reads the
  `True` as stronger than it is.

## Deviations

Built on `fix/18-github-file-structure`, commit `1d8e76e`. The approach, the files, and
the scope are as planned, and the test plan's expected results came out exactly as
written (`file_structure present: True`, 214 paths, `has_tests = True  has_ci = True`).
Three small differences from the plan:

1. **A fallback branch I did not plan for.** If the repo response has no
   `default_branch`, the tree request uses `HEAD`. I added it because passing `None`
   into the URL would request `/git/trees/None` and quietly fail. GitHub always sends
   `default_branch` for a real repo, so this only matters for malformed responses.
2. **The empty-repo case is covered by a mocked 409, not a real empty repo.** The
   failing-request test returns GitHub's `409 Git Repository is empty.` and asserts
   `file_structure == ""` with the other keys intact. That was the plan; I record it here
   because the risk line said I had not run it against a real empty repo, and I still
   have not.
3. **mypy is not fully clean on the test file, as the plan said it would be.**
   `agent/tools/github_tool.py` is clean, and the CI typecheck command
   (`mypy api/ core/ ingestion/ rag/ agent/ safety/`) passes with no issues. Running mypy
   on `tests/unit/test_github_tool.py` directly reports one `arg-type` error because
   `RepoAnalyzer.parse()` is annotated `str | bytes` while it really accepts a dict.
   That mismatch already exists and is already suppressed for `ingestion.pipeline` in
   `pyproject.toml`. CI does not typecheck `tests/`, and changing `parse()`'s signature
   is outside this issue's scope, so I left it alone. I did add `dict[str, Any]`
   annotations to the test's fixtures to clear a second, self-inflicted mypy error.

4. **A failed tree request logs a warning, which `_has_readme()` does not.** The plan
   said the new method would mirror `_has_readme()` and only log on truncation. In the
   build, a failed tree request also logs `github_tree_fetch_failed` with the error
   before returning `""`. I added it because the failure is otherwise silent, which is
   exactly how #18 went unnoticed: without the log, a rate-limited or empty repo would
   look identical to a repo with no tests.

None of this changes what the posted plan comment says, so no follow-up comment is
needed on the thread.
