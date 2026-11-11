# Contribution: demo --html: --run is silently ignored when an interrupt leaves no runs

Path: `beat-2-contribution/contribution-story.md`

**Contribution Number:** 1
**Student:** Mamadou Ba
**GitHub Username:** Mamadouba2004
**Issue:** https://github.com/amrevr/swarmEval/issues/21
**Pull Request:** https://github.com/amrevr/swarmEval/pull/30

This is written from what I ran and read on 2026-11-11. Nothing here is copied from a tool's claim without a check.

---

## Toolkit

Every story section below ends with a Toolkit block. The four dated entries come first.

**Unit 5: A wild-search scout, and a rubric patch it earned**

I built issue-scout, which searches all of GitHub with my written queries, ranks the results cheaply, and grades the top candidates against my rubric. My first scan accepted all five candidates, but four of them were repos created 0 to 2 days earlier with 0 to 1 stars where only the owner had committed (riskline, trainleak, fleet-sim, dense-melee-lab). They passed "Maintainer alive" and "Repo in use" exactly as written, so the rubric could not tell a showcase repo from a project with a reviewer. I patched "Maintainer alive" to fail a repo that is under 30 days old, has under 5 stars, and has only owner commits; the re-scan rejected those four and accepted dapr-agents #912 instead. Later, grading my own pick (pytorch#74602) showed a gap I had not patched: the rubric had no check for "already fixed on the default branch".

**Unit 6: Voice guard, repo-memory file, and a hook that went in late**

I folded my pr-precheck voice guide (the newest copy) into one guide at `~/.claude/voice-guard/voice-guide.md`, symlinked the three skills to it, and wrote the Upstream rules (claims, questions, pings) from my existing rules. I also copied the real-repo scopes into repro-check and plan-check, patched the scout rubric with the staff `scope-feasible` row plus one clause of my own (fail if the default branch already has the behaviour), and wrote `CLAUDE.local.md` for swarmEval (kept out of the diff with `.git/info/exclude`). What broke: Claude Code's permission system denied the step that merges the hook into `~/.claude/settings.json` ("Self-Modification") while I was doing the work, so for the whole contribution I ran the guard by hand (`check_voice.py --draft`), and it passed my claim comment and my PR description (both are in `guard-log.md`). After the story was first pushed I asked for the install directly and the hook went in (with a backup of settings.json). The wiring test `claude "run this command: gh issue comment --help"` was stopped, as expected; the output did not show which hook stopped it, and I assume it was the voice guard. The hook therefore did not check anything I posted. I also did not re-run the unit 1 harness against the gold labels after the rubric patch, so I do not know that the patch kept agreement.

**Unit 7: Ship-gate run by hand on the real branch**

I copied the ship-gate's files, swapped pr-precheck's scope for the real-repo scope (keeping `scope.md.sandbox`), and wrote `ship-manifest.md` for amrevr/swarmEval: install the dev dependencies, run `.venv/bin/pytest -q`, and one excluded row for lint (the repo configures none; ruff is only proposed in open PR #9). As with the voice guard, the hook merge was denied at first, so the gate did not run through `gh pr create` for this PR; I ran `check_gate.py --branch` by hand. It passed both repo checks (6s and 4s) and pr-precheck's four required checks, and its log has the fingerprints. It also pointed out two things I had to check myself, the fork and parallel PRs (I checked: the branch is on my fork, and no open PR touches `demo`). It suggested dropping a first line that repeated the title, which I did for the posted body. I installed the hook afterward with the install script (it saved a settings backup and left the voice guard's entry alone); `claude "run this command: gh pr create --help"` was stopped by the ship-gate, which says a `gh pr create` without `--title` and `--body-file` is stopped, so that wiring test passed. It came after the PR was open, so the hook did not gate PR #30.

**Unit 8: PR-monitor and a playbook written from a very small sample**

I installed the monitor and wrote `playbook.md` from the repo's real history: its only outside contributor had three PRs, with a first reply after 4 and 11 days, one merged after 19 days, and one still open at 14 days with no comments. From that I set 14 quiet days before one nudge. Something broke first: `--state` said "NO PR YET" until I set the branch's upstream to my fork, because `gh pr view` could not tie the branch to the PR. After that it read PR #30 and a full run drafted "NO REPLY": the only item in the thread is a Copilot notice that it could not review because of a quota limit, so there was nothing to answer. I posted nothing from the draft.

---

## Why I Chose This Issue

I started with the issue I had claimed on the AI301 site, pytorch/pytorch#74602. Before building anything I ran my scout on it and read the current code, and the scout rejected it: the bug is already fixed on `main` (`scatter_object_list` now uses `group.rank()` and `group_src` and documents `src` as the global rank), even though the issue is still open and labeled. My two written fallbacks were also dead: production-stack#667 and pytorch#121587 both have open competing PRs, and dapr-agents#912 from my earlier scan had been merged. So I switched. I ran a fresh scan with my four queries (384 unique results, 374 after my drop filters, one per repo, fewest comments first) and graded the top five. Two accepts were poor fits (an issue-templates task, and a toy-function task farm), two were rejected (a multi-PR GPU feature, and a one-day-old single-author repo), and I took amrevr/swarmEval#21, a small Python bug in a repo with a pytest suite, that needs no GPU or paid account.

Honest read of the scout: it accepted swarmEval, but that is the weak spot I had already patched once. The repo has 0 stars, and the owner wrote about 118 of 121 commits (one other contributor made 3). It passed only because the repo is 61 days old, so my 30-day cutoff is arbitrary; a better rule would ask for a merged PR from someone other than the owner. I also learned that the ranking cannot tell untouched issues from just-created ones. I patched the Scope check (see the Unit 6 entry) and have not yet patched the single-author weakness. I hope to learn how a maintainer I have never talked to reviews a small PR. My claim comment: https://github.com/amrevr/swarmEval/issues/21#issuecomment-6789134076

**Toolkit**

- Tool: issue-scout, in direct mode on pytorch#74602 and in scan mode with my unchanged strategy and my rubric.
- What it produced: a reject on pytorch#74602 (Scope fails, fixed on main), then a ranked scan whose top five gave three accepts and two rejects, and my pick amrevr/swarmEval#21. The read-outs are in `scan-output.md` and `scan-notes.md`.
- Difficulties: the rubric had no "already fixed on the default branch" check, so I caught the pytorch issue by reading the code. The ranking surfaces only brand-new issues, and swarmEval passes the single-author rule only because of its age.
- What changed: I added the "already fixed" clause to the Scope check. I would still change the single-author rule to require a merged PR by a non-owner. I did not re-run the unit 1 harness after patching.

---

## Understanding the Issue

**Problem Description**

`swarmeval demo --html FILE --run N` writes an HTML report and shows run number N in its graph and timeline. If I press Ctrl-C before the first run finishes, there are 0 runs, so the tool writes a summary-only page. It does this without saying that my `--run N` was ignored. When some runs did finish but N is out of range, it does print a note and skips the HTML, so the 0-run case is the one case that stays silent.

**Expected Behavior**

When `--run` was given explicitly and there are no runs to show, the tool should say so in one line (the issue suggests exactly that), so a user does not think `--run 5` took effect.

**Current Behavior**

On upstream `main` (f6ca0f5), interrupting `demo` before run 1 with `--html r.html --run 5` exits with code 130, writes `r.html`, and prints nothing about `--run` (stderr is just a newline).

**Affected Components**

`swarmeval/cli.py`: `_check_html_run`, `_write_html` and `cmd_demo`. The related tests are in `tests/test_audit_fixes.py`.

**Toolkit**

- Tool: repo-memory file (`CLAUDE.local.md`) and plain reading of the code.
- What it produced: the call path of `cmd_demo` in my own words, and the list of the files and tests that matter.
- Difficulties: the repo has no CONTRIBUTING.md, CLAUDE.md or AGENTS.md, so there was nothing from the maintainers to import. I did one read of `cli.py` only, not a whole-repo orientation.
- What changed: nothing.

---

## Reproduction Process

**Environment Setup**

- OS: macOS 15.8.2 (Darwin 24.6.0), x86_64.
- Python 3.14.7 (the project requires 3.12 or newer), pytest 9.2.0, swarmeval 0.1.0 installed editable into a venv. The project has no runtime dependencies; the dev extra is just pytest.
- Setup: `gh repo fork amrevr/swarmEval`, clone, `git switch -c fix/21-demo-html-run-note`, then `python3 -m venv .venv && .venv/bin/pip install -e '.[dev]'`. The README gives the same install line and `pytest -q`.
- Baseline: `.venv/bin/pytest -q` gave 293 passed, 1 skipped (the Parquet test, which skips without pyarrow).
- Challenges: the pip install and the test runs worked first time. The real obstacle was Claude Code's permission system: it denied `gh repo fork` and the venv/pip/pytest run on the upstream clone, so I ran those two commands myself in the terminal, and then it went through. It later also denied editing `~/.claude/settings.json` for the hooks, which I did not work around (see Toolkit entries for units 6 and 7).

**Steps to Reproduce**

1. Check out upstream `main` (f6ca0f5) with a venv and `pip install -e '.[dev]'`.
2. Simulate Ctrl-C before the first run, as the existing tests do (monkeypatch `research_swarm.make_swarm` to raise `KeyboardInterrupt`), then call `main(["demo", "--out", DIR, "--html", DIR/"r.html", "--run", "5"])`.
3. Observed: exit code 130, `r.html` written, stderr `'\n'`, nothing says `--run 5` was ignored.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/Mamadouba2004/swarmEval/commit/864865e (a test that fails on the unfixed code; this is on my fork's branch `fix/21-demo-html-run-note`).
- **Screenshots/logs:**

  ```
  $ .venv/bin/pytest -q tests/test_audit_fixes.py     # at 864865e
  FAILED tests/test_audit_fixes.py::test_demo_interrupted_before_the_first_run_notes_an_explicit_html_run
  1 failed, 33 passed in 0.38s
  exit code: 130 | html written: True | stderr: '\n'
  ```

- **My findings:** the bug reproduces exactly as the issue describes. I confirmed it with both the failing test and a direct call of `main`.

**Toolkit**

- Tool: repro-check's real-repo scope, plus my own failing test (the test is the reproduction).
- What it produced: a committed failing test and the before output above. I did not run the repro-check skill itself on a written repro report; I wrote the repro as a test.
- Difficulties: the permission denials above delayed the run until I ran the install myself.
- What changed: nothing.

---

## Solution Approach

**Analysis**

`_check_html_run` in `swarmeval/cli.py` checks `--run` against the planned number of runs before evaluation. After an interrupt, `cmd_demo` only warns when `0 < len(result.runs) <= run`. With 0 runs, that is false, so it falls through to `_write_html`, which writes the summary-only page and drops `--run` without saying so.

**Proposed Solution**

In `cmd_demo`, before `_write_html`, when `--html` is set, `--run` was given explicitly and the result has 0 runs, print one stderr line: `swarmeval: --run N ignored (no runs to show); wrote a summary-only HTML report`. The page is still written and the exit code stays 130. Deliberately not changing: `_check_html_run`, `_write_html`, `render_html`, `cmd_report` (it checks `--run` against loaded runs, so it cannot reach this state), the exit code, and the existing out-of-range handling when some runs finished. I also did not make it an error, since the issue suggests only a note.

**Implementation Plan**

1. Add a failing test for the interrupted-before-run-1 case with `--html --run 5` (exit 130, HTML written, note on stderr).
2. Add the note in `cmd_demo`.
3. Run the full suite.

Deviations: none. The exact wording of the note was settled while writing the test.

**Toolkit**

- Tool: plan-check with the real-repo scope, through my `plan.md` (the plan above plus Not in scope list).
- What it produced: `plan.md` for the ship-gate. I did not run plan-check as its own step; the ship-gate's pr-precheck read the plan.
- Difficulties: none met beyond the above.
- What changed: nothing.

---

## Testing Strategy

I re-ran the reproduction against the built change, and ran the repo's own suite.

- Before the fix (commit 864865e): the new test fails (1 failed, 33 passed in `tests/test_audit_fixes.py`). The direct run gives exit 130, HTML written, stderr `'\n'`.
- After the fix (commit 2650ac7): `tests/test_audit_fixes.py` gives 34 passed. The same direct run gives exit 130, HTML written, and stderr `'\nswarmeval: --run 5 ignored (no runs to show); wrote a summary-only HTML report\n'`.
- Full suite: 293 passed, 1 skipped on `main`, and 294 passed, 1 skipped after my change.
- Lint: the repo configures none, so I ran none.
- Not tested: Python versions below 3.14.

**Toolkit**

- Tool: the ship-gate with my manifest, run by hand (`check_gate.py --branch`), which runs the repo checks and then pr-precheck.
- What it produced: "PASS", with the install and the test suite both ok, and pr-precheck's four required checks all pass (log in `~/.claude/ship-gate/gate-log.md`).
- Difficulties: the hook was not installed at the time, so the gate was a manual run rather than a stop before `gh pr create`.
- What changed: I excluded the lint row with a dated reason instead of inventing a check.

---

## Implementation Notes

**2026-11-11 (picking)**

My claimed pick, pytorch#74602, turned out to be fixed on `main` already, and both fallbacks were dead, so I re-ran the scout and picked swarmEval#21. A wrong turn was cloning the course repo first: it was an empty folder, so I re-cloned it before doing anything else.

**2026-11-11 (setup)**

Forked amrevr/swarmEval, cloned it, renamed `origin` to `upstream`, and pointed `origin` at my fork. Branch `fix/21-demo-html-run-note`. The permission system denied the fork and the install, so I ran them myself.

**2026-11-11 (repro and fix)**

Found the existing helper `interrupt_demo_at_third_run` in `tests/test_audit_fixes.py`, wrote the failing test next to the related interrupt tests, and committed it (864865e). Then added the 3-line change in `cmd_demo` (2650ac7) and pushed the branch to my fork.

**2026-11-11 (checks, claim, PR)**

Wrote `plan.md`, `test_evidence.md`, `pr_draft.md` and `ship-manifest.md`, kept out of the diff via `.git/info/exclude`. The voice guard passed my PR description and claim; the ship-gate passed. I checked upstream before posting: no assignee, 0 comments, and no open PR touching `demo`. I read the claim and the PR text and approved both, then I posted the claim and opened PR #30.

**Toolkit**

- Tool: my toolkit as a whole (scout, voice guard by hand, ship-gate by hand, PR-monitor), with the repo-memory file carrying the repo's details between steps.
- What it produced: the artifacts named in the sections above.
- Difficulties: the two hooks were not installed while I worked (the permission system denied the settings edit), so they never fired on their own. I installed them afterward at my request.
- What changed: I ran each guard by hand and recorded in each Toolkit entry that I did, then installed both hooks after the PR was open.

---

## Pull Request

**PR Link:** https://github.com/amrevr/swarmEval/pull/30

**PR Description:** Title: "fix(cli): note an ignored --run when an interrupted demo leaves no runs". Body: `Fixes #21`; what changed (one stderr line, page still written, exit code still 130, only when `--run` was explicit); the changes (3 lines in `cmd_demo`, one test in `tests/test_audit_fixes.py`) and what I did not change; testing (new test fails before, 294 passed after, baseline 293 passed, 1 skipped; no linter configured; I only tested on Python 3.14.7); and an AI disclosure paragraph (I used Claude Code to trace the bug, draft the test and fix and write the description, and I ran the tests and reviewed every changed line).

**Maintainer Feedback:**

- 2026-11-11: none yet from a human. The only item in the thread is a notice from `copilot-pull-request-reviewer` that it could not review because of a quota limit. The PR-monitor read it and drafted "NO REPLY", and I did not respond.
- 2026-11-11: nothing to address. The playbook sets 14 quiet days before one nudge, so the earliest is about 2026-11-25.

**Status:** Awaiting review. The PR is mergeable, with no checks reported (the repo has no CI).

**Toolkit**

- Tool: pr-precheck (via the ship-gate) and the PR-monitor.
- What it produced: pr-precheck accepted the PR (4 required checks pass, and the preferred honesty check passes). The monitor read PR #30 and drafted no reply.
- Difficulties: the monitor said "NO PR YET" until I set my branch's upstream to my fork; the feedback label came from a bot's "commented" state, not from any real feedback.
- What changed: I set the upstream, and the monitor's draft explained why it was not a real review, which is the case my playbook's response rules did not cover yet. I would add a rule about bot notices.

---

## Learnings and Reflections

**Technical Skills Gained**

I traced a CLI code path (`cmd_demo`, `_check_html_run`, `_write_html`) and learned how the repo simulates Ctrl-C in tests with a monkeypatched factory that raises `KeyboardInterrupt`. I also used `gh` to check an issue's state, linked PRs and timeline, to fork and clone, and to open a PR from a fork. I learned how `gh pr view` ties a branch to a PR through the branch's upstream.

**Challenges Overcome**

My first pick was already fixed upstream, and I only found out because I read the code before building. The permission system blocked the fork, the install and the hook edits, which cost time; I ran the first two myself and did not work around the hook block; the hooks went in later when I asked for them directly. Picking a replacement showed the scout's single-author weakness again, which is the main thing I would still patch.

**What I'd Do Differently Next Time**

I would check an issue's state and the current default-branch code before claiming it on the course site, and I would add that check to the rubric earlier. I would install the hooks first and in a normal terminal so they actually fire, and run the unit 1 harness after every rubric patch.

---

## Resources Used

- The issue itself, https://github.com/amrevr/swarmEval/issues/21: it named the function, the file and the suggested one-line fix.
- `tests/test_audit_fixes.py` in swarmEval: the existing interrupt tests and the `interrupt_demo_at_third_run` helper showed me how to write the new test.
- The swarmEval README's Install section for the venv and test commands.
- The CodePath starter repos ai301-unit6-starter, ai301-unit7-starter and ai301-unit8-starter (their README and INSTALL.md files) for the voice guard, ship-gate and PR-monitor.
- pytorch/pytorch#74602 and its timeline, and the current `distributed_c10d.py` on `main`, which showed that issue was already fixed.
