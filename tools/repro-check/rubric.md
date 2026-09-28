# Rubric: is this reproduction package ready to post?

Six required checks and two preferred ones. The required checks cover the proof
families: the environment is recorded, the steps are runnable by a stranger, an
artifact exists, the artifact is faithful to the issue it claims, the claim comment
is specific and modest, and the words respect the repo's stated conventions.

Every check grades the thing itself, never the write-up's shape. A terse report with
an environment line, exact commands, and an output excerpt is complete. A long,
well-formatted report with no artifact is not.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `environment-recorded` | The repro report's environment record: the lines naming where the attempt was run. In a bundle, the report's environment section or inline environment line. | The report names both the platform (OS, or OS plus shell/driver where the issue is platform-specific) **and** the version, release, or commit of the software under test. One line is enough. Grade `fail` when neither is stated anywhere in the report, or when the issue's behaviour depends on a setting (driver, build profile, install method) that the record omits. | required |
| `steps-runnable-by-stranger` | The repro report's steps, read against what the package itself supplies. | Every input the steps need is either included in the package or publicly obtainable: the commands, the input file contents, and any config. Grade `fail` when a step depends on something the reader cannot get (a private repository, an unshared config file, a local dataset), or when the steps omit the trigger the issue names so that following them would not exercise the bug. | required |
| `artifact-present` | The repro report's artifacts: command output, log excerpt, error text, screenshot, or measurement produced by running the steps. | At least one concrete artifact from an actual run appears in the report. Grade `fail` when the report only asserts what happened ("I confirmed this", "guaranteed reproducible", "I verified the race condition") without showing anything a reader can inspect. A described observation is not an artifact. | required |
| `artifact-matches-issue` | The artifacts read against the behaviour the issue describes, plus the version and trigger the steps actually used compared with the version and trigger the issue targets. | For a report claiming reproduction: the artifact shows the specific behaviour the issue describes, produced by the issue's own trigger, on a version the issue targets. Grade `fail` when the artifact shows an adjacent or different failure (a graceful validation error standing in for a crash, a compile error standing in for a runtime error, output corruption standing in for a process death), when the steps alter the issue's trigger, or when the version or platform differs from the issue's and the report does not call the deviation out. A report that states it **could not** reproduce grades `pass` on this check when it shows its real attempt and names what differed; an honest cannot-reproduce is a good outcome, not a failure. | required |
| `claim-is-specific-and-modest` | The claim comment. | Both hold. **Specific:** the comment names something particular to this issue (the symptom, the file or component, or what the author will investigate), so that it could not be pasted unchanged onto another issue. **Modest:** it promises only investigation or a report. Grade `fail` on interchangeable boilerplate ("I'd like to work on this, please assign me"), on a bare me-too or +1 with no stated intent, or on promising a fix, a pull request, or a delivery date. | required |
| `conventions-respected` | The repo-facts block's contribution-policy line and any stated issue or comment template, read against the text of both comments. | The comments satisfy the repo's stated conventions. Grade `fail` when the policy requires disclosing AI assistance and neither comment discloses it (course packages are AI-assisted work), or when the repo states a comment rule the package plainly breaks. A policy that is silent on AI, or permissive without a disclosure requirement, grades `pass`: silence is not a requirement. | required |
| `control-run-shown` | The repro report's artifacts. | The report includes a contrasting run: the same steps on an input, version, or setting where the behaviour does **not** occur, showing the boundary of the bug. | preferred |
| `next-step-stated` | The closing lines of the claim comment or the repro report. | The author names a concrete next action (what they will look at, test, or check), rather than ending on the observation alone. | preferred |

## Verdict rule

`accept` (ready to post) if every `required` check grades `pass`. Any `required`
check grading `fail` produces `reject` (hold), and so does `unclear` on a required
check: proof you cannot verify is proof that is not ready to go upstream.

The one exception is the claim-only draft state described in `SKILL.md`. Checks whose
evidence is the repro report (`environment-recorded`, `steps-runnable-by-stranger`,
`artifact-present`, `artifact-matches-issue`, and `control-run-shown`) are reported
`unclear` with evidence `not yet applicable: claim-only draft` and left out of the
verdict rule. The verdict then rests on `claim-is-specific-and-modest` and
`conventions-respected` alone, and answers only whether the claim comment is ready.

`preferred` checks never change the verdict. Report their grades, and on an accepted
package mention them as what makes the report stronger than the minimum.
