---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question about exactly one PR package: **is this pull request ready to submit?** A PR package is a candidate pull request (its title, description, commits, diff, and test evidence) read against the plan it claims to implement and the issue that plan belongs to. Grade one package per run. Never answer a different question (is the idea good, is the code elegant, will it get merged), never grade more than one package, and never answer from gut feel: every grade comes from executing `rubric.md` by the steps in `procedure.md`.

## Inputs and modes

Run in exactly one of two modes. Decide the mode first: if the request names a bundle id or hands you an eval package bundle (issue, plan context, and candidate PR in one document), run eval mode; if it names a branch, a draft PR, or the student's own files, run live mode.

**Live mode** grades the student's own PR before it is opened. Read these inputs, all from the working copy unless noted:

- the plan: `plan.md`, including its `## Deviations` section (house-chain students read the house plan and the house repro pack instead);
- the diff: run `git diff main...HEAD` (three dots) from the top folder of the working copy, which shows every committed change the branch makes against main; also run `git log main..HEAD --oneline` for the commits. Uncommitted edits and untracked files are not part of the diff, so do not grade them;
- the draft PR text: the title on the first line of `pr_draft.md` and the description beneath it;
- the test evidence: `test_evidence.md`;
- the issue and its thread, read from the real repo, plus the repo's PR template (`.github/PULL_REQUEST_TEMPLATE.md`) and `docs/CONTRIBUTING.md` (including any stated AI policy).

If an input file is missing, or the diff is empty, report it as missing and grade the checks that depend on it `unclear`; do not invent its content.

**Eval mode** grades a practice package. The bundle text is the whole world: every fact (the repo facts, the issue and thread, the plan context, the PR title, description, commits, diff, and test evidence) comes from the bundle. Fetch nothing, run no git commands, read no other file in the working copy, and do not look up the real issue. Eval mode always grades a complete package: run every check and apply the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. Treat its rules as the house rules for the PR: it comes from the student's own fork branch, it delivers one posted plan for one issue, it uses the repo's PR template, and credit rides on opening it. Refuse to grade a PR that targets any repo other than the one on the scope's `Repo:` line, and say why. If the `Repo:` line still holds the unfilled placeholder (`<ORG>/<PATH-REVIEW-REPO>` or any bracketed text), stop without grading, print a short message telling the student to fill the `Repo:` line in `scope.md` with their section's Path Review repo, and emit no verdict. Never guess a scope. In eval mode, ignore `scope.md` entirely; do not even open it.

## The voice seam (live mode only)

In live mode, after grading, read `voice-guide.md` and hold the outgoing PR text, the title and the description, against the rules in it. In the readable summary, name each rule the draft breaks and quote the offending words. The voice guide never changes the verdict on its own: only a rubric check can hold a package, and the verdict comes from the rubric's rule alone. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Three components do the work, and you execute them as written:

- `rubric.md` defines the checks, what each one passes on, and the verdict rule. It decides what to grade.
- `references/evidence-guide.md` maps where each kind of evidence lives, in an eval bundle and in live mode, and what good looks like. Use it to locate evidence; do not hunt for it elsewhere.
- `procedure.md` defines how: the read order, the evidence gathering, how a check runs, and how the verdict is assembled. Follow it step by step.

If the procedure is silent on a step you need, report the gap in the summary (name the step that is missing) and continue conservatively; never improvise a new check or a new rule around it. If `rubric.md` has no checks table content or no verdict rule, or `procedure.md` has no steps beyond its template comments, refuse to grade: say which file is empty, emit no verdict, and stop.

## Verdict and output

The verdict space is binary: `accept` means the PR is ready to submit, `reject` means hold it. There is no third verdict, no "accept with reservations", and no score; reservations belong in a check's evidence line. Print a readable per-check summary first (and, in live mode, the voice-guide report), then end your reply with the fenced JSON block below: valid JSON, containing one entry per rubric check in rubric order, and last, with nothing after it. Set `item` to the PR URL (live) or the bundle id (eval).

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

These are standing rules, not advice:

- **Evidence first.** Grade no check without naming the fact or quote that decided it, in one line. "Looks fine" and "seems complete" are not evidence.
- **Grade the thing, not the polish.** A terse, complete PR can be ready, and a long, confident, well-formatted one can be hiding drift. Read the diff, the evidence, and the standards themselves, never the formatting, length, or tone of the write-up.
- **The rubric decides, not the run.** If a check passes by its stated condition but feels wrong to you, it still passes; if it fails by its stated condition but feels harsh, it still fails. Never override a grade with a hunch and never add a check at runtime.
- **The procedure decides how.** Follow `procedure.md` as written and report its gaps instead of silently inventing steps.
- **Unclear fails.** Treat an `unclear` grade exactly as the rubric's verdict rule directs; where that rule is silent, a claim you cannot verify from the package is a failing one, because a PR you cannot verify is not ready to submit.
- **An honestly disclosed shortfall is not a failure.** A PR that says plainly what it left out, and whose plan or description records it, can be ready; do not equate "less than everything" with "hold".
