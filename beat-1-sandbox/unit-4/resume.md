# Unit 4 resume note

Updated: 2026-10-07

This is a handoff note for finishing Unit 4 after the CodePath Claude Sonnet usage
limit is increased. It is not a replacement for the graded `pull-request.md` or the
harness-generated `eval-run.txt`.

## Current state

- Coursework submission repo: `https://github.com/BrandenBedoya/ai301-coursework`
- PathReview PR: `https://github.com/codepath/pathreview-ai301-fa26-s1/pull/104`
- PR branch: `fix/18-github-file-structure` on `BrandenBedoya/pathreview-ai301-fa26-s1`
- PR is open against `codepath/pathreview-ai301-fa26-s1:main`.
- All five GitHub CI jobs passed: `lint`, `typecheck`, `test-unit`, `test-integration`,
  and `frontend`.
- The PathReview fork branch is pushed and clean at `1d8e76e`.
- The coursework `main` branch is pushed and clean at `88c0635` when this note was
  written.
- The current installed skill is at `~/.claude/skills/pr-precheck/`; its graded files
  have matching copies in `tools/pr-precheck/` in the coursework repo.
- Local PR preparation files `plan.md`, `pr_draft.md`, `test_evidence.md`, and related
  repro files are in the PathReview checkout. The draft and evidence files are
  intentionally excluded through `.git/info/exclude`; their relevant contents are
  already reflected in the open PR and this write-up.

## Evaluation status — do not submit the stale run

Three completed full runs before the final rubric adjustment scored:

1. `18/20`: disagreed on `pkg-19` and `pkg-20`.
2. `19/20`: disagreed on `pkg-04`.
3. `19/20`: disagreed on `pkg-05`.

Focused `--only` reruns then established these results on the later skill revisions:

- `pkg-01`, `pkg-04`, `pkg-05`, `pkg-07`, `pkg-19`, and `pkg-20` each matched their
  gold labels on their relevant focused rerun.
- Most recently, `pkg-04` rejected, while `pkg-05` and `pkg-19` accepted, all matching
  their gold labels.

After those revisions, a confirming full run was attempted. Claude Sonnet completed
five package calls, then the service returned the individual spend-limit error
(`You've hit your individual spend limit`). The harness marked the remaining 15
packages as errors and correctly did not save that partial run.

The `eval-run.txt` in the starter eval folder is still a complete **older** 19/20 run.
Its fingerprints are:

```text
SKILL.md          443d562fdbe32505
rubric.md         c0fadf02a4253be6
procedure.md      05001e4026125ab3
evidence-guide.md 98d6d43e07870a7a
```

The current installed files fingerprint as:

```text
SKILL.md          443d562fdbe32505
rubric.md         a42acad82d4894fc
procedure.md      342ad18ac59aa7c5
evidence-guide.md 40393b61d50b9ac4
```

The old run disagrees on `pkg-05`, so it does not evaluate the current rubric and must
not be copied into the coursework submission folder as the final run. The current
skill still needs a complete Sonnet run; the target is at least 18/20 and at least one
match in every category.

## Resume steps

1. Confirm CodePath raised the Claude usage limit. The previous CLI status was logged
   in, but a simple `claude -p --model sonnet` request failed with the spend-limit
   message. No credentials or tokens need to be shared.
2. Run the official full evaluation from the starter's `eval/` folder:

   ```bash
   cd ai301-unit4-starter/eval
   /Users/brandenbedoya/Workspace/codepath/codepath-ai/ai301-capstone-project/.venv/bin/python \
     run_eval.py --skill /Users/brandenbedoya/.claude/skills/pr-precheck \
     --save-run eval-run.txt
   ```

3. Check the full-run score and `categories:` floor. If revisions are needed, use
   `--only` for disagreements and add a previously agreeing canary from each small
   category a loosened check might affect. Run another full pass with `--save-run`
   after the final edits. Never hand-edit the generated run.
4. Run the live precheck from the PathReview checkout:

   ```bash
   cd pathreview
   claude "pr-precheck: grade my draft PR for issue https://github.com/codepath/pathreview-ai301-fa26-s1/issues/18: plan in plan.md, the diff on this branch, title and description in pr_draft.md, test evidence in test_evidence.md"
   ```

   Confirm the installed scope names `codepath/pathreview-ai301-fa26-s1`, the run
   reads `git diff main...HEAD`, and its final fenced JSON contains the four rubric
   checks. The PR is already open; this is the assignment's required precheck step
   being completed late, not a new PR.
5. Copy the newly generated full run to
   `ai301-coursework/beat-1-sandbox/unit-4/eval-run.txt`. Update
   `pull-request.md` so the run history ends at that exact saved score, and add the
   final focused/full-run trade-off details. The current skill copy is already in
   `tools/pr-precheck/`; if any skill file changes, copy the whole installed skill
   folder there again first.
6. Commit and push the updated `eval-run.txt` and `pull-request.md` in the coursework
   repo. Then update/resubmit the coursework repo root URL through the portal if the
   portal permits or requests a refreshed submission.

## Verification completed before pausing

On the PathReview branch: `make test-unit` passed with 378 passed and 53 xfailed;
`make lint` passed; `make typecheck` passed with no issues in 76 files.
`make test-integration` produced the expected `no tests ran` result because there are
currently no integration tests. The real-code reproduction changed from no
`file_structure` and `False/False` on `main` to 214 paths and `True/True` on the fix
branch. All five GitHub CI jobs subsequently passed.
