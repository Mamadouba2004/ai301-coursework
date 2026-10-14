# Procedure: how this tool grades a PR package

## Read order

1. Read the repo facts first (eval: the repo-facts block; live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`). Note every section or item the template requires, any changelog or doc entry it demands, and the AI-use policy (disclosure required, human-authorship only, or none stated). These are the standards the description will be held to.
2. Read the issue and the thread highlights next. Note the reported symptom in one sentence, and note any explicit direction from a maintainer, owner, or member (a named cause, a location, or an approach).
3. Read the plan before the diff (eval: the plan-context block; live: `plan.md`). Write down the plan's in-scope list, its not-in-scope list, the files it says it will touch, each approach step, its test plan (the repro steps it will re-run and the outcome it expects), and every deviation note. Reading the plan first fixes the yardstick; a diff read first makes whatever it contains look like the plan.
4. Read the candidate PR's diff and commit list (eval: the Diff and Commits sections; live: `git diff main...HEAD` and `git log main..HEAD --oneline`). List every changed file and, for each file, what each hunk does in one phrase. Note anything that is not the fix.
5. Only after steps 1-4, read the PR title and description, then the test evidence. Read the description last so its claims cannot color what you saw in the diff; read the evidence last so you can compare it against the plan's test plan, which you already hold.

## Evidence gathering

- **Diff matches the plan**: pair the diff's file list from step 4 against the plan's files and scope from step 3. Mark each changed file and hunk as inside the plan, covered by a deviation note, or neither. Then tick off each promised approach step as present in the diff, absent but disclosed, or absent with no word. Finally list every concrete claim in the description (files touched, docs updated, tests added, "exactly as planned", "no other changes") and mark each true or false against the diff.
- **Test evidence**: take the repro steps and expected-after outcome from the plan's test plan (step 3). Find, in the test evidence, the command or action that re-runs them and the output shown before and after. Record whether that evidence runs the path the fix changes or only an unchanged or control path, whether any added test targets the fix, and whether each check the plan or repo facts name as required (tests, lint, type check) has its outcome shown.
- **Diff quality**: from the hunk list in step 4, record every debug print or log line, commented-out line or block, dead or unused function, import, or variable, TODO about the change, and every hunk that only reformats, re-indents, or reorders imports, or touches an unrelated area. Record "none" if there are none.
- **Template and policy**: for each requirement noted in step 1, find where the description satisfies it (the section, the issue reference, the checklist, the entry in the diff) and note whether it has real content or is empty boilerplate. Find the AI-disclosure sentence if the policy requires one and note whether it names the tool and the extent. Check the maintainer direction from step 2 against the plan and description.
- **Honesty**: list what the PR says it left out or deferred, in the description or deviation notes, and what the diff or evidence shows it did not finish.

## Check execution

1. Grade the checks in rubric order: Diff matches the plan, Test evidence is decisive, Diff is reviewable, Repo template and policy are honored, then Honesty.
2. Grade each check from the evidence gathered for it, applying the rubric's pass condition literally. Do not re-read the whole package for each check; go back to the package only to settle a specific missing fact.
3. Grade the thing, not the polish: ignore length, formatting, tone, and commit style except as the rubric's condition makes them evidence.
4. Where the diff and the description disagree, the diff is the truth. Where the plan and the diff disagree and a deviation note ties them together, the note settles it; with no note, it is drift.
5. If the evidence for a check is genuinely absent from the package (for example the live test evidence file is missing), grade it `unclear` and name what is absent. Do not use `unclear` for a hard call: make the call and name the fact that tipped it.
6. Write each check's evidence as one line quoting the deciding fact: a phrase from the diff, plan, description, evidence, or repo facts. Never write a paraphrase such as "looks good".

## Verdict assembly

1. Apply the rubric's verdict rule exactly: `accept` only if all four required checks grade `pass`; otherwise `reject`.
2. Count any required check graded `unclear` as `fail`. The Honesty check is graded and reported but never changes the verdict.
3. If more than one required check failed, quote the first failing required check in rubric order as the deciding check in the readable summary, and list the other failures after it.
4. In live mode, add the voice-guide report after the check summary: each rule the title or description breaks, with the offending words quoted, or "no voice-guide rules broken". This never changes the verdict.
5. End with the fenced JSON block from SKILL.md: one entry per rubric check in rubric order, the verdict last, nothing after the block.
