# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: in an eval bundle, the plan's stated cause sits in the `## Candidate plan` section's opening "Cause:" or "Diagnosis:" line. The evidence to check it against is the `## Repro evidence` block's steps, any artifact (traceback, log, output excerpt), and especially any line labeled "Control:" — a control run is the single strongest piece of evidence, because it isolates one variable and either implicates or rules out a mechanism directly. In live mode, the plan's cause is in the student's draft `plan.md`, and the repro evidence is the student's own posted repro comment from week 2 (or the house repro pack).

What good looks like: the stated cause explains every artifact and control run in the repro evidence without any of them contradicting it. A control run that reproduces the symptom with the blamed mechanism absent or unchanged rules that cause out, full stop — no amount of thread agreement or polish recovers a diagnosis a control run has already ruled out. Watch specifically for a plan that adopts a cause the thread already seems to favor: thread consensus is not evidence, and the repro evidence's own control runs are read independently of what anyone in the thread believes.

## Scope

Where it lives: in an eval bundle, the `## Candidate plan` section's "Scope:" or "Change:" line, including any explicit "In:" / "Out:" or "In scope:" / "Not in scope:" split. The real scope is also the plan's "Approach:" section — read what it actually lists doing, not just what the scope line claims.

What good looks like: one bounded change that addresses the issue's reported symptom, with specific files or call sites named, and an explicit line excluding other work. A deferred secondary concern, named and reasoned about ("I'm leaving X for a follow-up because..."), is still in scope — that is honest scoping, not scope creep. Scope creep looks like a short, isolated root cause described in one sentence, followed by an approach section that lists a redesign, a migration, a new abstraction, a new config option, or a framework/test-harness change the issue never asked for — often confidently written, sometimes with the correct core fix buried inside it.

## Executability

Where it lives: the `## Candidate plan` section's "Approach:" or numbered-steps list, naming files, functions, or code locations.

What good looks like: a stranger with no access to the author's head could start making the change today — specific files or locations, a concrete mechanism, and an order of steps. A plan fails this even when confidently written if its real decisions are deferred to build time: "recover() somewhere," "fix upstream or vendored, whichever is easier," "not sure if this is gocui or tcell," "poke around the editor code this weekend." No named file plus no chosen approach is the clearest failure shape; investigating-instead-of-doing is the same failure in softer words.

## Test plan

Where it lives: the `## Candidate plan` section's "Test:" or "Test plan:" line.

What good looks like: a plan to re-run (or adapt) the specific steps from the repro evidence and a named, observable outcome that would not have held before the fix — a color that flips, an exit code that changes, a crash that no longer occurs on the same reduced test case, a value that now matches. A vague or generic test plan is the clearest failure shape: "run the full test suite," "should feel fast," "nothing else should feel broken" names no observable outcome tied to the actual fix, even when the rest of the plan is strong.

## Honesty

Where it lives: the `## Candidate plan` section's "Risk:" or "Risks and unknowns:" line, and, in live mode only, a `## Deviations` heading in the student's `plan.md` after a build.

What good looks like: a real uncertainty (an unmeasured performance cost, an untested platform, whether a related code path shares the same bug) stated as an open question, not asserted as resolved. A recorded deviation that says plainly what changed and why, in the author's own words, is honest work regardless of whether the change was large or small; "nothing changed" is just as honest when true. This check does not need a plan to hedge things it has genuinely nailed down — grade whether the stated unknowns are honest, not whether unknowns exist.

## Comms

Where it lives: the `## Candidate plan comment` section, read against two places — the `## Thread highlights` block (for maintainer direction) and the `## Repo facts` block's stated contribution policy and AI-use policy line.

What good looks like: when a thread highlight is attributed to an OWNER, MAINTAINER, MEMBER, or CONTRIBUTOR role and states a cause, a location, or a proposed/validated approach, the plan comment engages with it — follows it, builds on it, or explicitly explains a deviation — rather than silently pursuing a different angle the thread has already moved past. Separately, when the repo-facts block's contribution policy states an AI-use disclosure requirement (look for "must be disclosed," "state the tool used," "all AI usage... must be disclosed"), the plan comment must contain a disclosure sentence; every comment graded under this skill is course-authored, so it is always AI-assisted for this check's purposes, and a comment with no disclosure line never satisfies a repo that asks for one, no matter how good the plan otherwise. A repo whose policy only asks that comments be human-authored/understood, with no explicit disclosure line demanded, is satisfied by a comment that is specific and in the commenter's own words, same as weeks 1-2. A repo with no stated AI policy at all, and a thread with no maintainer comments at all, needs neither signal — grade this check `pass` in that case, since there is nothing to have ignored or omitted.
