# Rubric: is this plan ready to post and build from?

Six required checks and two preferred ones. The required checks cover the failure
families that get bad plans posted: a diagnosis the repro evidence rules out, a fix
wrapped in a redesign, a plan a stranger could not start, a test plan with no
observable outcome, a comment that ignores what a maintainer already said in the
thread, and a comment that skips an AI disclosure the repo requires.

Every check grades the plan itself, never the write-up's shape. A terse plan with a
grounded cause, one named site, an in/out line, and a repro re-run with a stated
result is complete. A long, confident, well-sectioned plan can still be wrong,
unbounded, or unbuildable.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diagnosis-fits-evidence` | The plan's stated cause (its diagnosis line or section), read against **every** step and control run in the repro-evidence block, and against the change the plan proposes. | The plan states a cause, that cause is consistent with every step and control the repro evidence shows, and the proposed change acts at that cause. Grade `fail` when any repro step or control run rules the stated cause out (a control shows the blamed component working, or shows the damage already present before the blamed step runs), when the plan dismisses part of the evidence as a "red herring" without showing why, when no cause is stated at all, or when the change works around the symptom (docs, a catch-all, a suppression) while the evidence and plan both point at a code cause the change leaves untouched. Agreeing with the issue or the thread is not enough: the repro evidence is the test. | required |
| `one-bounded-change` | The plan's scope statement, its list of proposed changes, and its files or areas, read against the behavior the repro evidence shows. | Every proposed change is needed to make the reproduced behavior correct. Applying the same fix to sibling sites of the identical defect, adding regression tests, or auditing for the same pattern and reporting (not fixing) what turns up all count as in scope. Grade `fail` when the plan also includes work the reproduced bug does not need: a refactor or rewrite, a dependency migration or version upgrade, a new option, setting, or prop, a module restructure, a test-harness migration, a CI matrix, or a fix for a different symptom "while in the area". A correct core fix bundled with any of these still fails. Work the plan names as deferred, split out, or filed separately does not count against it. | required |
| `stranger-can-start` | The plan's files or areas and its approach steps. | A stranger could begin the change without asking the author anything: the plan names where the change goes (a file, function, or a specific named code path) **and** commits to one approach for what the change does there. An unknown about the exact line or one layer up or down, stated as an unknown inside an already-named area, still passes. Grade `fail` when the plan defers a real decision to build time: no file or code path named, a choice left open between alternatives ("upstream or vendored, whichever is easier", "not sure which layer"), a change placed "somewhere", or steps that are investigation only ("profile it", "figure out what changed", "optimize whatever turns up"). | required |
| `test-plan-observable` | The plan's test plan, read against the repro evidence's steps and their recorded output. | The test plan names a concrete run (the repro steps, the repro command, or a test encoding them) **and** the specific result expected after the fix, stated so a reader could compare it with real output and say pass or fail: an output value or line, an exit code, a match, a color, a count, or a threshold, which differs from what the repro evidence recorded as the bug. Grade `fail` when the only stated outcomes are subjective or general ("should feel fast", "should work correctly", "nothing else should feel broken", "looks much better"), or when the plan only runs an existing test suite and names no outcome for the reproduced behavior itself. | required |
| `follows-thread-direction` | The thread highlights, filtered to comments by OWNER, MEMBER, COLLABORATOR, or CONTRIBUTOR authors, read against the plan comment and the plan. | If such a comment gives direction (names the culprit code, states the agreed fix or rejects one, posts a patch or test build, opens a fixing PR, or calls the behavior intended), the plan comment engages it: it follows that direction, or names it and says why the plan differs. Grade `fail` when the comment proceeds as if that direction was never posted, or silently picks an approach the maintainer rejected or swaps their code fix for a different kind of change. When no comment from those roles gives direction, grade `pass`. | required |
| `ai-disclosure-where-required` | The repo-facts block's contribution-policy line, read against the text of the plan comment. Every package is treated as AI-assisted work. | Grade `fail` only when the policy requires AI use to be disclosed in issues or comments, or requires disclosure of all AI usage in any form, **and** the plan comment contains no disclosure. Grade `pass` when the policy is silent on AI, permits AI with responsibility or review rules, or asks for disclosure only in pull requests. A policy requiring comments to be written by a human in their own words is an authorship rule, not a disclosure rule: it grades `pass` unless the comment itself says it was AI-written. | required |
| `risks-and-unknowns-named` | The plan's risk, unknown, or open-question lines. | The plan names at least one specific risk or open question about its own change, or states that it found none and why. | preferred |
| `regression-test-added` | The plan's files and test plan. | The plan adds an automated test that encodes the reproduced case, so the bug cannot quietly return. | preferred |

## Verdict rule

`accept` (ready to post and build from) if every `required` check grades `pass`. Any
`required` check grading `fail` produces `reject` (hold). `unclear` on a required check
also produces `reject`: a plan you cannot verify from the package is a plan that is not
ready to build from.

`preferred` checks never change the verdict. Report their grades, and on an accepted
package name them as what makes the plan stronger than the minimum.
