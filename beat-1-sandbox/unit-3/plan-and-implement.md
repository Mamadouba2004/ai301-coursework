# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

Mamadouba2004

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/69#issuecomment-5986840179

Following the fix shape the issue itself describes (and what jacho15, wazimmerman, ssmitpatel, jaskhetani, Natkuma01, saif0524, and pgazar's reproductions all converge on): I'm scoping this to a single `isinstance(data, dict)` guard at both `json.loads` call sites in `parse_review_output` (the fenced json branch and the raw-JSON branch), so a non-dict parsed value — a top-level array, per my week-2 repro — falls through to the existing `_parse_plaintext_output` path instead of reaching `_parse_json_output` and crashing on `.items()`. I'll remove the `@pytest.mark.xfail(strict=True)` marker on `test_json_array_fallback` as part of the same change, since it has to come off once the test passes for real, and I'm adding one more regression test for the fenced-array path specifically, since ssmitpatel's and pgazar's reports show that's a separate call site the existing test never exercised.

Out of scope: the unrelated seeded defect noted in a comment at the top of `parse_review_output` about a lost accumulator — that's a different issue, and I'm not touching it here.

Test plan, now built and verified: re-ran my week-2 repro commands (the direct unfenced-array call, plus the fenced-array variant from the thread) against the fix — both now return a `list[FeedbackSection]` (one `general_feedback` section) instead of raising `AttributeError`. The full `tests/unit/test_output_parser.py` suite passes 20/20 with the xfail marker gone: `test_json_array_fallback` passes for real, and the new `test_json_array_fallback_in_fence` covers the fenced-input call site. Branch is up on my fork: `fix/69-json-array-fallback`.

Drafted with Claude Code assistance under CodePath's AI301 program; I reviewed and understand the analysis and the fix above.

---

## Your branch

**Branch**

fix/69-json-array-fallback

**Evidence**

Before (from my week-2 repro, re-confirmed against the pre-fix branch state):

```
$ python3 -c "
import json
from rag.generator.output_parser import parse_review_output
raw = json.dumps(['First feedback item', 'Second feedback item'])
print(parse_review_output(raw))
"
Traceback (most recent call last):
  File "<string>", line 5, in <module>
    print(parse_review_output(raw))
  File ".../rag/generator/output_parser.py", line 48, in parse_review_output
    return _parse_json_output(data)
  File ".../rag/generator/output_parser.py", line 68, in _parse_json_output
    for key, value in data.items():
AttributeError: 'list' object has no attribute 'items'

$ python3 -m pytest tests/unit/test_output_parser.py -v
...
tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback XFAIL
...
18 passed, 1 xfailed in 0.18s
```

After (same commands, run against the built fix on `fix/69-json-array-fallback`):

```
$ python3 -c "
import json
from rag.generator.output_parser import parse_review_output
raw = json.dumps(['First feedback item', 'Second feedback item'])
print(parse_review_output(raw))
"
2026-10-04 21:03:06 [warning  ] raw_json_not_a_dict            json_type=list
2026-10-04 21:03:06 [info     ] plaintext_output_parsed        content_length=47
[FeedbackSection(section_name='general_feedback', content='["First feedback item", "Second feedback item"]', confidence=0.7, suggestions=[])]

$ python3 -c "
import json
from rag.generator.output_parser import parse_review_output
raw = '```json\n' + json.dumps(['First feedback item', 'Second feedback item']) + '\n```'
print(parse_review_output(raw))
"
2026-10-04 21:03:07 [warning  ] json_fence_not_a_dict          json_type=list
2026-10-04 21:03:07 [warning  ] raw_json_parsing_failed
2026-10-04 21:03:07 [info     ] plaintext_output_parsed        content_length=59
[FeedbackSection(section_name='general_feedback', content='```json\n["First feedback item", "Second feedback item"]\n```', confidence=0.7, suggestions=[])]

$ python3 -m pytest tests/unit/test_output_parser.py -v
...
tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback PASSED
tests/unit/test_output_parser.py::TestOutputParser::test_json_array_fallback_in_fence PASSED
...
20 passed in 0.15s
```

---

## Eval iterations

**Run history**

One run occurred: the first full run against the finished rubric, evidence guide, and
procedure hit the bar on the first attempt — 20/20 scored items agree, with every category
matched (clear-accept 7/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3,
wrong-cause 4/4). This score matches the agreement line in the committed `eval-run.txt`
exactly. No revision loop was needed, so there is nothing to retry or re-run with `--only`.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#11261, category `thread-convention`). Gold label: reject.
My rubric's verdict: reject. The candidate plan here is genuinely strong on every other
axis — it follows the thread's already-proposed direction (a page generation counter,
recomputing `prev` only when it changed), names exact files and an ordered approach, and
has a decisive test plan (both fuzz-derived test cases passing, a no-hyperlink control
unchanged). My rubric's Diagnosis, Scope, Executability, and Test-plan checks all passed
it. It fails on exactly one required check: Comms. Ghostty's `AI_POLICY.md` states "All AI
usage in any form must be disclosed, stating the tool used and the extent of the
assistance," and the candidate plan comment contains no disclosure sentence at all. My
rubric's Comms check treats every graded comment as AI-assisted by default (since the
course's own comments are), so silence never satisfies an explicit disclosure requirement
— it reads this package correctly as a reject on disclosure alone, matching the gold note
("excellent bounded plan... but the comment contains no AI-use disclosure and ghostty's
stated policy requires disclosing all AI usage").

**Check rationale**

Quoted exactly from the `rubric.md` I uploaded to `tools/plan-check/`:

"When the thread contains explicit maintainer direction (an owner-identified cause or
location, or an approach the owner proposed or already validated), the plan comment
engages with and follows that direction rather than pursuing an unrelated angle the thread
has moved past. Separately, when the repo-facts block states an explicit AI-use disclosure
requirement, the plan comment includes a disclosure statement; every plan comment graded
here is course-authored and therefore AI-assisted, so silence never satisfies a stated
disclosure requirement, no matter how good the plan is otherwise."

I wrote it with two independent failure branches, rather than one combined condition,
because the eval set's two `thread-convention` packages each test a different branch:
`pkg-04` fails on ignoring the thread's direction (the owner had already pinpointed the
exact file and posted a working patched binary, and the candidate plan went with a
docs-only workaround instead), while `pkg-20` fails on the disclosure branch alone (it
follows the thread correctly but omits a required AI disclosure). A single merged
condition risks only catching whichever failure mode I happened to design around first; I
rejected that shape specifically because the category has only two scored packages, so a
check blind to either branch would still miss one of them and fail the category floor.
This check passed both packages correctly on the first full run, so nothing needed
revising here.

**Trade-offs**

The one check in my rubric that gives something up on purpose is the Honesty check, which
I made `preferred` rather than `required`: a plan that is bounded, grounded, executable,
and decisively tested still gets accepted even if its stated risks lean more confident than
fully hedged. I accept that this check cannot flip a verdict by itself. I confirmed nothing
in the scored set actually depends on it being required: all 20 scored packages' gold
verdicts are decided by the five required checks alone, and the two packages built around
false confidence specifically (`calib-03`'s operator-swap trap and `calib-04`'s
"run the full test suite" borderline) are both unscored calibration packages, not scored
ones — so making Honesty required would not have changed a single scored verdict, while
making it preferred correctly keeps it from over-rejecting an otherwise-ready plan for tone
alone. The trade-off is real but inert on this particular 20-package set; it would start
mattering only if the scored set grew to include a package whose sole defect was
overstated certainty with everything else in order.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
