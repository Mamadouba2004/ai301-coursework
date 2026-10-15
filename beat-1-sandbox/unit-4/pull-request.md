# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/214

**Branch**

fix/69-json-array-fallback

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these fields.

**Run history**

One full run: 19/20. The last (and only) score matches the agreement line in the committed eval-run.txt: "agreement: 19/20 scored items  (bar: 18/20: PASS)". Category line from the same file: "categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3", so every category met its floor on that run.

**Package analysis**

pkg-11 (clear-accept, BurntSushi/ripgrep#3477, escaped trailing space in .gitignore). The gold label is accept; my rubric decided reject. eval-run.txt shows the miss as: "pkg-11  clear-accept  accept  reject   NO     failed: Diff matches the plan, Honest about shortfalls".

Why my rubric read it that way: the check "Diff matches the plan" requires that "every concrete claim in the description (a file, a doc, a test, a behavior, ...) is true of the diff". The pkg-11 description says "tests added for the escaped, unescaped, and double-backslash cases", but the diff adds one test, escaped_trailing_space_is_kept, whose assertions cover the escaped-space case and the double-backslash case; there is no separate unescaped-trailing-space assertion. My rubric treated that overclaim as a failed concrete claim, and because it is also a claim the description makes that the diff does not back up, the preferred Honesty check graded fail too. The gold label treats it as a small wording slip around an otherwise faithful, tested, clean, policy-compliant fix. I think the rubric's reading is defensible but stricter than the gold, and this is the one package I knowingly miss.

**Check rationale**

The check I am quoting is "Diff is reviewable", with its pass condition exactly as it reads in tools/pr-precheck/rubric.md:

> The diff shows the fix and nothing that buries it: no debug prints or logging left in, no commented-out code or abandoned first attempts, no dead or unused functions or imports, no TODO notes about the change itself, no formatting, re-indent, or import-reordering churn, and no unrelated hunks. Fails if any such debris or churn appears in the diff, even when the core fix inside it is correct and well evidenced. Commit message style never decides this check; only what the diff contains does.
>
> Why it reads that way: reviewability is a separate failure mode from drift. "Diff matches the plan" asks whether the work is the planned work; this check asks whether the work is visible to a reviewer, so a correct fix with a stray debug print or a re-indent still fails it. The last sentence is there because commit message style is a repo-convention question that the template and policy check already owns; keeping it out of this check stops one flaw from failing two checks and keeps this one about what the diff contains. I rejected folding reviewability into "Diff matches the plan" because the two fail on different evidence (hunks versus the plan's scope pair), and a merged check would have hidden which one a package failed.
>
> **Trade-offs**
>
> The cost of this rubric is strictness on description claims. Because "Diff matches the plan" fails any concrete description claim that the diff does not back up, it will reject a PR like pkg-11 whose fix is correct but whose description overstates its tests by one case; that false reject is the one disagreement in the run (clear-accept 6/7). I accept that cost on purpose: a description that promises more than the diff delivers is what silent drift looks like, and silent-drift scored 4/4. I did not loosen the clause after the run because the run already cleared the bar (19/20) and every category floor, and softening the claim-truth wording to flip pkg-11 risked flipping a silent-drift package the other way with no way to know without another full run. Nothing else in the rubric changed after the run: the other 19 packages matched their gold labels on the first run, so there was no other disagreement to explain.
> 
