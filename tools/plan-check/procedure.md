# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first (title, body, labels) and note the reported symptom in one sentence: what behavior is wrong, and what correct behavior would look like.
2. Read the repro-evidence block next, before the plan. Note every step, artifact, and control run it contains, and which specific mechanism or code path each one points to or rules out. This list is what the Diagnosis check will compare the plan's stated cause against, so build it before reading anything the plan claims.
3. Read the thread highlights. Note whether any comment is explicit maintainer direction (an owner/maintainer naming a cause, a location, or an approach, or confirming one already works) as opposed to a reporter's speculation or a non-maintainer's comment. Only maintainer direction counts for the Comms check.
4. Read the repo-facts block. Note the stated AI-use disclosure policy (explicit disclosure required, human-authorship-only required, or no stated policy) and any contribution-template asks.
5. Only after 1-4, read the candidate plan in full, then the candidate plan comment. Reading the evidence first prevents the plan's own confident framing from substituting for a check against what the repro evidence and thread actually establish.

## Evidence gathering

- **Diagnosis**: from the plan, extract the single stated cause in one sentence. From the repro-evidence notes (step 2 above), list every artifact, step, or control run that bears on that cause, whether it supports or rules it out.
- **Scope**: from the plan, extract the in-scope statement and any not-in-scope line, and list every file, area, or kind of change the plan actually describes doing (not just what it says it's doing). A plan can declare itself "one bounded change" while its approach section lists several; grade the actual list of changes, not the declared scope line.
- **Executability**: from the plan's approach section, list the concrete files/areas named and the ordered steps given. Note any point where the plan defers a decision ("whichever," "maybe," "somewhere," "not sure") instead of making one.
- **Test plan**: from the plan's test plan section, extract the stated expected observation. Compare it against the repro-evidence steps noted in step 2: does re-running (or adapting) those specific steps produce the stated observation, or is the test plan generic and disconnected from those steps?
- **Comms**: from the plan comment, check whether it mentions or responds to the maintainer direction noted in step 3 (if any exists), and whether it contains an AI-use disclosure statement (if the repo-facts block requires one).
- **Honesty**: from the plan's risks/unknowns section and any `## Deviations` heading, list what is named as uncertain versus what is asserted as fact.

## Check execution

1. Grade checks in the order they appear in `rubric.md`: Diagnosis, Scope, Executability, Test plan, Comms, then Honesty.
2. Grade each check using only the evidence gathered for it in the previous stage — do not re-read the whole package per check.
3. A check is `unclear` only when the package genuinely contains no evidence to decide it either way (for example, the repo-facts block states no AI policy and the thread has no maintainer comments at all, so there is nothing for Comms to fail or pass on beyond "needs neither" per the rubric's own pass condition — in that specific case grade `pass`, since the rubric's pass condition already defines the no-signal case as passing). Reserve `unclear` for when the rubric's pass condition requires a fact the package does not supply at all, not for a borderline judgment call — make the call instead and name the evidence that tipped it.
4. When a required check's evidence directly contradicts the plan's own framing (for example, the plan calls itself "one bounded change" but its approach section lists a second unrelated area of work), grade on the actual list of changes gathered in evidence-gathering, not the plan's self-description.
5. Quote the exact fact or line that decided each check in the `evidence` field of the output: a short phrase from the repro evidence, the plan, or the thread — never a paraphrase like "looks fine" or "seems grounded."

## Verdict assembly

1. Apply the rubric's verdict rule exactly: accept only if Diagnosis, Scope, Executability, Test plan, and Comms all grade `pass`.
2. If any of those five grades `fail`, the verdict is `reject`, regardless of how the others graded.
3. Treat any required check graded `unclear` as `fail` for verdict purposes, per the rubric's stated rule.
4. The Honesty check's grade is always reported but never changes the verdict; include it in the output's checks array regardless.
5. In the output JSON's checks array, quote the single deciding check's evidence line again in the readable summary shown before the JSON block, so the summary and the JSON agree on why the verdict landed where it did.
