# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the plan's scope, files, approach steps, and any deviation or deferral note sit in the `## Plan context` block; what the PR actually changed sits in the candidate PR's `### Diff` (file headers and hunks) and `### Commits`; the description's claims sit in `### Description`. In live mode, the plan and its `## Deviations` section are in your `plan.md`, the diff is the output of `git diff main...HEAD` (and `--stat` for the file list) on your branch, and the claims are in `pr_draft.md` beneath the title.

What good looks like: every changed file and hunk falls inside the plan's stated scope or a deviation note, every promised piece is in the diff or its absence is stated in the description or a deviation note, and every concrete claim in the description is true of the diff. Silent drift looks like two things: more than the plan (a new flag, a config option, a rename pass, a rewrite of a nearby function, while the description says "exactly as planned" or "no other changes"), or less than the plan (the plan promises a warning and a docs update, the diff has only the warning, the description says the docs now cover it). A deferral the plan names and the description restates is honest, not drift.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the `### Test evidence` section, read against the plan's test plan and repro evidence in the `## Plan context` block and against any checks named in the repo facts. In live mode, `test_evidence.md` next to `plan.md`, read against the test plan in `plan.md`, with the repro output from week 2 as the before and the same steps on your branch as the after; the repo's own checks (`make test-unit`, `make test-integration`, `make lint`, `make typecheck`) with their output pasted.

What good looks like: the plan's own repro steps are re-run on the path the fix changes, with the output before and the output after, and the difference is observable (a value, an exit code, a crash that no longer happens). Each required check's outcome is shown; a check that fails or stops with "no tests ran" is reported honestly with the reason. Weak evidence looks like "tested locally, works now, tests pass" with nothing shown, evidence that exercises only the control or unchanged path (a GET when the bug is in a POST, one file when the bug needs two), or an added test that covers something other than the fix.

## Diff quality (harness category: unreviewable)

Where it lives: in an eval bundle, the `### Diff` (unified diff) and `### Commits` list. In live mode, `git diff main...HEAD` (read every hunk, not just `--stat`) and `git log main..HEAD --oneline`.

What good looks like: the fix is visible and nothing rides along. Debris tells: a debug print or leftover log line, a commented-out line or abandoned first attempt, a dead or unused function or import behind an allow(dead_code) or a leading underscore, a TODO about the change itself, a re-indent or reformat hunk, an import reorder, and hunks in files unrelated to the fix. Commit messages like "wip" or "cleanups while debugging" corroborate debris but never decide the check; only what the diff contains does.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the `## Repo facts` block (the stated PR-template asks and the contribution policy, including any AI-use policy) and the `## Thread highlights` for maintainer direction, read against `### Description` and `### Diff`. In live mode, `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the Path Review repo, the issue thread on GitHub, and your `pr_draft.md`.

What good looks like: every section or item the template or guide requires is present with real content (the issue reference as Closes #N, the checklist, the changelog or doc entry, and any required entry that must appear in the diff is actually in the diff), and, where the repo states that AI use must be disclosed, the description has a disclosure sentence naming the tool and how much it did. Every package and every course PR is AI-assisted, so a missing disclosure never passes against a repo that asks for one. A maintainer's named cause, location, or approach is followed or its departure explained. Boilerplate left empty, an unticked required checklist, a missing required entry, or an absent disclosure is the wall. A repo with no template and no AI policy asks for nothing here. Whether the description's claims match the diff is plan fidelity, above, not this section.
