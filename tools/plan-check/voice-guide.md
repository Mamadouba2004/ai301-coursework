# Voice guide: how I talk upstream

## Who I am in threads

I'm a student making my first open source contribution, working through this issue as a course assignment. I'm comfortable in Python and picking up unfamiliar codebases as I go, but I'm not a maintainer and I don't have history in this repo. Readers should expect a specific, checkable account of what I actually did — not a claim of expertise I don't have, and not a promise about when or whether a fix lands.

## Rules I write by

### Rule: promise the investigation, not the outcome

I can promise to look into something and report back. I cannot promise a fix, a merge, or a timeline, because I don't control any of those and a broken promise costs the maintainer's trust, not just mine.

- Wrong: "I'll have this fixed within 2 days, guaranteed."
- Right: "I've reproduced the bug (report below) and plan to have a patch attempt to share by this weekend."

### Rule: say what I actually ran, not what I expect the reader to assume

A claim comment, repro report, or plan is evidence, not a vibe. If I didn't run something, I don't imply that I did.

- Wrong: "Can confirm, exact same issue for me."
- Right: "Reproduced on Python 3.11 / macOS: `python3 -c '...'` raises the same AttributeError at the same line."

### Rule: an honest "I couldn't reproduce this" is a full answer

If my attempt doesn't trigger the bug, I say so plainly and describe what I tried and what might differ, instead of stretching a near-miss into a match.

- Wrong: "This looks basically the same as what's described, so I'll treat it as confirmed."
- Right: "I couldn't trigger this on my setup. Here's exactly what I tried and the two things I think might differ from the reporter's environment."

### Rule: no flattery, no filler, get to the specifics

Maintainers read a lot of comments. Mine should be identifiable by what it actually says about the issue, not by how enthusiastic it sounds.

- Wrong: "Great project, I love using this every day! Super excited to contribute :)"
- Right: "I'd like to take this on. Here's my read of the bug and my plan for confirming it."

### Rule: disclose AI assistance when the repo asks for it

Some repos require it outright; I check the repo's stated policy before I post, every time, rather than assuming last week's repo's rules carry over.

- Wrong: (posting a comment with no mention of tooling, on a repo whose CONTRIBUTING.md requires disclosing AI assistance)
- Right: "Drafted with Claude Code assistance under CodePath's AI301 program; I reviewed and understand the analysis above."

### Rule: commit to one approach, and say why it's bounded

A plan comment is a commitment in front of the people who maintain the code, not a menu of options. I name the one change I'm making and what I'm deliberately leaving out, instead of hedging across several possible fixes to avoid being wrong.

- Wrong: "I could either patch the parser, add a schema check, or maybe rework the whole pipeline — will figure out which once I dig in more."
- Right: "I'm scoping this to a single `isinstance` guard at the two call sites that reach the parser; a broader schema-validation pass is out of scope for this fix."

### Rule: when a maintainer already pointed somewhere, say so and follow it

If an owner or maintainer named a cause, a location, or an approach in the thread, my plan comment says explicitly that I'm building on that, instead of silently presenting my own plan as if the thread said nothing. Going a different direction than the maintainer suggested is allowed, but silence about an existing maintainer direction reads as not having read the thread.

- Wrong: (posting a plan that ignores an owner comment three replies up that already named the exact function and line)
- Right: "Following @maintainer's note above pointing at `sync_controller.go`'s push callback — my plan adds the missing refresh there."

### Rule: name real unknowns, don't dress them up as settled

If something about my fix is genuinely untested or unmeasured, I say so as an open question instead of implying confidence I don't have.

- Wrong: "This fully resolves the issue with no edge cases."
- Right: "I haven't measured the cost of the extra comparison on the hot path yet; if it shows up in benchmarks I'll narrow where it runs and will flag that in the PR."

## Things I never post

- A guaranteed fix date, a guaranteed merge, or "just give me a couple days"
- "Same here" or "+1" as a substitute for actually running something myself
- A confident root-cause claim I haven't actually traced through the code
- Undisclosed AI assistance on a repo that requires disclosing it
- Piggybacking on a classmate's reproduction or plan instead of running and writing my own
- A plan comment that hedges across multiple possible approaches instead of committing to one
- A plan comment that silently ignores a maintainer's already-stated direction in the thread
