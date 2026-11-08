# Script Review — Real Qwen3-14B run audit

**Run ID:** `20261108T081419_789562`  
**Source:** exact unedited uploaded ZIP (`sha256 d93de70218ea26bb462c942ba3387ba7e034f30b8ed3e0e3acf3b2b2b455fc96`)  
**Scope:** six short **synthetic** story examples, not Kuku's full show scripts.  
**Model:** `Qwen/Qwen3-14B` revision `40c069824f4251a91eefaf281ebe4c544efd3e18`; pipeline `desk-2.1`; 4-bit NF4 across 2 × Tesla T4.  
**Status:** `completed`; **processing error count:** `0`. No editor reviews recorded (`human_reviews.json` is empty).

## Actual run telemetry

| Measure | Value |
|---|---:|
| Episodes processed | 6 |
| Model candidate questions | 7 |
| Generation calls | 14 |
| Wall time | 877.77 s (14 min 38 s) |
| Model generation time | 585.31 s (9 min 45 s) |
| Model load time (environment metadata) | 250.25 s (4 min 10 s) |
| Input tokens | 9980 |
| Generated output tokens | 4687 |
| Processing errors | 0 |
| Human-reviewed decisions | 0 |

A single extraction call, `E209_extract_0`, exhausted its 2,048-token budget after **253.08 seconds**, triggering one successful format-repair retry. This one call consumed about **43%** of total generation time. The run ended without a pipeline error because the retry succeeded. Time and resource usage are material limitations for a production workflow.

## Case-level audit against original expected *open* categories

| Case | Human-expected OPEN categories | Model-produced OPEN categories | Category agreement | Semantic notes |
|---|---|---|---|---|
| 101 — Pending offer | decision | decision | Yes | Ren has not responded; `E101_H1` is appropriately open. |
| 102 — Refused offer | none | none | Yes | `E102_H1` correctly records explicit refusal as resolved, not open. |
| 201 — Ferryman resemblance | identity | identity | Yes | `E201_H1` asks about the **ferryman**, not the character hearing the rumour; no confirmation. |
| 205 — Countdown false alarm | none | none | Yes | `E205_H1` correctly sees that Sami exits safely and the threat was false. |
| 206 — Roof rescue | danger | danger | Yes | Rescue is not shown; `E206_H1` is open. However the words **“before the fire spreads”** go beyond what is explicitly shown. |
| 209 — Viewer knows guest | none | none | Yes | Both model candidates are labeled closed, but **one is semantically wrong** (details below). |

**Category-only agreement on this small development set: 6/6.** This is *not* full accuracy, question precision, evidence relevance, or reliable out-of-sample performance. The examples are very short, the rules overlap with the development prompts, and the 209 error shows why category agreement is insufficient.

## Important observed failure: E209_H2

The source explicitly says the narrator tells the viewer the disguised guest is Queen Eira, while the steward asks her name, gets no answer, and still wonders who she is. The generated question **“Will the steward determine the guest's identity?”** is classified as **`resolved_in_episode`** with the claimed answer that the guest declines to answer him.

This does **not** establish the broader question has been conclusively answered. It establishes only that one request for her name was declined. The steward remains ignorant at the end. It is also outside the intended narrowly defined *direct decision* category: asking someone's name is not a choice to accept/reject a concrete offer. Better behavior would be to **omit this candidate as out of scope**, or, under an expanded character-knowledge feature, leave the steward's knowledge question unresolved with correct evidence. The model's valid passage IDs do not make its interpretation correct.

`E209_H1` ("Is the guest Queen Eira?") is correctly labeled closed from the viewer's perspective because the narrator explicitly identifies her, but it adds an unnecessary already-known identity fact. The scene is a good adversarial test of **viewer versus character knowledge**.

## Engineering and evidence checks

- All 6 parsed episodes appear in `results.json` with the matching episode IDs.
- All 24 referenced setup and verification evidence IDs exist in their respective `parsed_episodes.json` episode. **Valid IDs: 24/24.** This is structural validity, **not semantic evidence quality**.
- All 14 generation-call JSON files are present, plus one extraction validation-error record from the recovered attempt.
- Recorded source-module hashes, environment versions, prompt text, outputs, run configuration, and raw model responses are preserved in the original ZIP.
- The full run does **not** test a long real episode or multiple episodes in sequence. It does **not** establish series-level payoff tracking, editor time savings, retention gains, or generalization.

## What to show in the 3-minute video

1. Show `E201_H1` or `E206_H1` to demonstrate a correct open candidate with cited lines.
2. Show `E102_H1` or `E205_H1` to demonstrate that a refused offer or false alarm is appropriately recognized as **resolved**.
3. Show `E209_H2` as an honest failure, and demonstrate an editor rejecting or correcting the output in Gradio.
4. Open the raw ZIP and show `results.json`, `review.csv`, `parsed_episodes.json`, and `calls/` to establish auditability.

## Next engineering improvements

- Make verification question-specific: an **unanswered future knowledge goal** is not resolved simply by an earlier refusal to reveal a name.
- Enforce the supported scope: decision = a concrete offer/request to **do** something, not an arbitrary factual question or a character's thought.
- Prevent ungrounded time constraints in the question generator ("before the fire spreads").
- Bound extraction thinking and add cheaper abort/retry logic; benchmark p50/p95 inference on realistic length scripts.
- Add independent, editor-reviewed annotation and compute exact-question/evidence precision, recall and false positive rates, not only category agreement.

**Submission note:** `results.json` and `review.csv` are *model-generated proposals*, not approved editorial decisions. The original run ZIP is provided without rewriting any predictions. The evaluation comments here are a separate human-style audit.