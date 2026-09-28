# Unit 1 — Issue Selection

## Run history

**PENDING — needs the actual `eval` output from my own machine.** The eval harness
requires the `claude` CLI, and I haven't finished a full run yet. I'll update this
section with: how many runs it took, which issue(s) disagreed with gold on the first
pass, what I changed in the rubric in response, and the final agreement/category-floor
line, then re-commit before this is truly final.

## Issue analysis

Walking through `issue-09` (`conda/conda#7617`, category `clear-accept`, gold verdict
`accept`) against my rubric's "Nobody already on it" check, because it's the one issue
in the set that's designed to trip up a naive claim-check:

The bundle shows a comment from `MesaJonathan` in **2022-01-20**: *"I'd like to take a
swing at this as my first open-source contribution."* A rubric that treats any claim
comment as disqualifying would reject this issue — and get it wrong. My check's pass
condition is explicit that a claim comment only blocks the issue if it's recent; a
claim comment older than 180 days that never produced a PR does not block. Here the
comment is ~4.5 years stale, a `github-actions[bot]` marked the issue stale the
following year with no further activity, and the `linked PRs` field shows only
`conda/conda#11627 (closed)` — no open PR ever came out of that claim. So the check
grades `pass`, the other four required checks pass too (active repo, bounded feature
request, no AI-policy statement), and the rubric's verdict is `accept`, matching gold.
The gold note agrees: "the 2022 claim is stale and the maintainer invited takers."

## Check rationale

Quoting the "Nobody already on it" row as written in `tools/issue-select/rubric.md`:

> Fails if an assignee is set, or any linked PR is open. A closed or merged linked PR,
> or a claim comment older than 180 days that never produced a PR, does not block the
> issue. (Path Review house rule in scope.md overrides claim comments in live mode
> only — it never overrides an assignee or an open linked PR.)

I picked this wording deliberately: the eval set (issue-09 above) needed the
"stale claim doesn't block" carve-out, and Path Review's own house rule (classmates'
claim comments never block, but an open linked PR still does) needed the same
distinction — "closed/merged PR or stale comment passes, assignee or open PR fails" —
to hold in both modes without contradicting itself.

## Trade-offs

- **Bot-authored commits.** The "Maintainer alive" check treats a bot-authored merge
  commit as evidence of life only if its message names a human's branch/PR. This
  correctly reads a bot-heavy commit list as alive when it's a bot merging a human's
  PR, but a repo whose last 5 commits are *all* dependency-bump commits with no human
  PR named would incorrectly read as dead.
- **180-day threshold is a single number covering two different questions** (is the
  maintainer active, is the claim stale). Every eval issue happened to fall clearly on
  one side of 180 days either way, so I didn't need two separate thresholds — but a
  live issue near that boundary would need a human judgment call the rubric doesn't
  make for you.
- **Scope check has no numeric threshold** (unlike the other four checks) — it's
  necessarily a qualitative read of "is the design settled." That's the least
  mechanically reproducible check in the rubric.

## Selection rationale

Graded three open, real Path Review candidates in live mode:

- `codepath/pathreview-ai301-fa26-s1#69` — accept
- `codepath/pathreview-ai301-fa26-s1#64` — accept
- `codepath/pathreview-ai301-fa26-s1#61` — accept

All three passed every required check: the repo pushed as recently as 2026-09-16
(human commits, not archived), none carry an assignee or an open linked PR (Path
Review's claim-comment house rule waives the several "I'll take this" comments on all
three), none hit the scope failure modes (no umbrella issues, no open design debates),
and `docs/CONTRIBUTING.md` states no AI-contribution policy at all, so nothing to fail
there.

Ranked by fit (per `scope.md`): **#69 first.** It's a real logic bug — `output_parser.py`
calls `.items()` on a parsed JSON array instead of a dict — that requires tracing an
actual call path (`parse_review_output` -> `_parse_json_output`) through code I didn't
write, which is exactly the "real debugging, reading someone else's codebase" practice
I said I wanted, not just editing a test fixture. It also only has one other student
circling it (a claim comment, no PR yet), versus three already elbow-deep on #61.
#64 ranked second — clean and bounded, but the actual "bug" is a bad assertion in a
test fixture, less debugging depth. #61 ranked third: valid and bounded, but the
fix is a one-line sqlalchemy.text() wrap, and it's the most contested of the three
(three classmates already reproducing it).

## Issue link

**Selected: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69**
("Output parser crashes on a top-level JSON array fallback")

Not claimed — per the assignment, choosing is not claiming; that's Unit 2's work.

## Verdict output

```json
[
  {
    "item": "codepath/pathreview-ai301-fa26-s1#69",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Last default-branch commit 2026-09-16 by Aburke225 and claude (human accounts), 11 days before today"},
      {"name": "Repo in use", "grade": "pass", "evidence": "Not archived; last push 2026-09-16, well within 180 days"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single bounded AttributeError in output_parser.py with named files, repro, and 2-4h estimate; no design debate"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "Assignees: none; Development: 'No branches or pull requests'; jacho15's claim comment doesn't block under the Path Review house rule"},
      {"name": "Contribution workflow allowed", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no AI-contribution policy; repo lists 'claude' as a contributor account"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labeled 'good first issue' and 'tier-1'"}
    ],
    "verdict": "accept"
  },
  {
    "item": "codepath/pathreview-ai301-fa26-s1#64",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Same repo-facts as #69: last commit 2026-09-16, human-authored"},
      {"name": "Repo in use", "grade": "pass", "evidence": "Not archived; last push 2026-09-16"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "Single test-fixture correction in test_relevance_scorer.py; one clear ask, no ambiguity"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "Assignees: none; Development: 'No branches or pull requests'; two claim comments (Cael-Pairrett, rishabhlingam) don't block under the house rule"},
      {"name": "Contribution workflow allowed", "grade": "pass", "evidence": "Same CONTRIBUTING.md, no AI policy stated"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labeled 'good first issue' and 'tier-1'"}
    ],
    "verdict": "accept"
  },
  {
    "item": "codepath/pathreview-ai301-fa26-s1#61",
    "checks": [
      {"name": "Maintainer alive", "grade": "pass", "evidence": "Same repo-facts: last commit 2026-09-16, human-authored"},
      {"name": "Repo in use", "grade": "pass", "evidence": "Not archived; last push 2026-09-16"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "One-line fix: wrap 'SELECT 1' in sqlalchemy.text() in api/routes/health.py; bounded, though it alone won't make /health return 200 (separate redis issue #62 also needed)"},
      {"name": "Nobody already on it", "grade": "pass", "evidence": "Assignees: none; Development: 'No branches or pull requests'; three claim/repro comments (smtanaka00, RadEagle, ONESO-goat) don't block under the house rule"},
      {"name": "Contribution workflow allowed", "grade": "pass", "evidence": "Same CONTRIBUTING.md, no AI policy stated"},
      {"name": "Good-first-issue signal", "grade": "pass", "evidence": "Labeled 'good first issue' and 'tier-1'"}
    ],
    "verdict": "accept"
  }
]
```
