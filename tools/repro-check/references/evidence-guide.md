# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: in an eval bundle, the repro report's own environment line (usually the first line or two of "Candidate repro report"), read against the issue body and the repo-facts block's stated latest release/target. In live mode, the student's draft repro report, read against the real issue's body, comments, and the repo's release page.

What good looks like: concrete tool/library version, OS, and any runtime versions the issue's bug depends on (e.g. a shell, a driver, a compiler). If the tested version or platform differs at all from what the issue targets, the report says so explicitly and explains why the result still counts (or doesn't). Silence about a known version gap is the failure mode this check exists for — it is not the same as stating the gap and reasoning about it.

## Steps

Where it lives: the repro report's numbered steps or command block, in an eval bundle or a live draft.

What good looks like: commands or configuration a stranger could run starting from a clean checkout or a public tool (a public playground link, a package install), with every input the issue calls out as necessary to the trigger actually present (the right flag, the right data shape, the right platform). A step that says "in my internal repo" or "using our config" without including that resource fails this even if every other step is precise. A step that changes the issue's trigger condition (a different operator, a different flag, a smaller/larger input than the one that actually matters) also fails here, even if it looks superficially similar.

## Behavior shown

Where it lives: the output excerpt, log lines, screenshot description, or exit code shown in the repro report, read side-by-side with the issue's stated symptom (its exact error text, exception type, exit code, or visual defect).

What good looks like: the artifact shows the same failure the issue describes — same error class, same exit code, same visual defect — not a different error the trigger happened to produce instead. Watch for three specific traps: (1) a graceful, handled error (a validation message, a normal non-zero exit) presented as if it were the reported crash/panic/abort; (2) an artifact that only shows the tool running normally, with no sign of the reported defect at all; (3) a result produced on a version/config the issue doesn't target, presented as if it spoke to the issue's own (different) reported behavior.

## Honesty

Where it lives: the repro report's stated conclusion — usually an explicit "Expected" / "Actual" pair or a closing sentence — read against every artifact actually included in that same report.

What good looks like: every strong word ("confirmed," "verified," "guaranteed," a named root cause) has a specific artifact right next to it that shows that exact thing. An honest "I could not reproduce this" is a full pass when it's backed by a real, described attempt and a stated hypothesis for the gap — it is not a lesser answer than a successful repro. The failure mode is confidence without an artifact: "+1, exact same for me," "I verified this race condition," "guaranteed reproducible" with nothing shown, or restating the issue's own words back as if they were freshly observed.

## Comms

Where it lives: the candidate claim comment's own text, and the repo-facts block's contribution-policy and AI-use-policy lines (in live mode: the repo's CONTRIBUTING.md / AI_POLICY.md and issue templates).

What good looks like: a claim comment that names the actual issue and states a real, specific next step in the commenter's own voice — not generic praise ("great project!"), not a template anyone could paste onto any issue, and no promised deadline or guaranteed fix (the honest move is to promise the investigation, not the outcome). Separately, check the repo's stated AI policy: if it requires disclosing AI assistance, the comment must disclose it plainly — a repo with no stated policy, or one that only asks for human review/understanding, is not the same as one that requires disclosure, and only the latter fails an undisclosed comment.
