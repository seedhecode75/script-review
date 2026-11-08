# Three-minute screen recording — speaking script

**Target: ~2:55–3:00 at a natural speaking pace.** Read in your own voice; the screen actions are **cues, not lines to read aloud**. Preload a completed real run because live inference can take many minutes. Use synthetic cases.

### 0:00–0:40 — Why I chose this problem
**SCREEN:** GitHub README title, then the architecture guide's “The problem we chose” section.

“Hi, this is **Script Review**, my submission for Kuku's AI Generalist assignment, Problem 3.

I chose this one because I wanted to do some research and identify a problem in Kuku's actual content workflow instead of only implementing a predefined task. Microdramas use frequent cliffhangers to make viewers want the next episode, and Kuku is increasingly using AI across storytelling and production.

That made me ask a question: **what if a supposed cliffhanger is already answered, is unclear, or relies on a detail that the story never properly establishes?** Repetitive or confusing hooks could contribute to viewer fatigue. I haven't measured that effect at Kuku — it's the problem hypothesis I wanted to explore.

So I built a tool to help an editor check the **actual question and its evidence**, rather than guess whether an ending is good.”

### 0:40–1:30 — Notebook and complete architecture
**SCREEN:** Open [Architecture Walkthrough](./ARCHITECTURE_WALKTHROUGH.md) section **2** and scroll the diagram. Briefly show notebook headings: source unpacking, environment/model setup, batch run, Gradio launch.

“The pipeline starts when we upload one or several scripts — TXT, JSON or a ZIP. Python parses each episode, preserves timestamps and dialogue, and assigns IDs to the original passages so every result is traceable.

For each episode, we inspect its ending — the last 45 seconds for timed scripts, or the last 12 lines for plain text. **Qwen3-14B**, running on two Kaggle T4 GPUs with 4-bit quantization, extracts concrete story setups such as a pending decision, an identity mystery or immediate danger.

Instead of asking the model to freely invent questions, Python builds short questions from structured subject-and-action fields. Then the model checks the **full text of that episode in chunks** for two separate things: does the story support this question, and has the story already answered it?

Python verifies the referenced lines and combines the findings into **open, resolved or needs-review** outcomes. This works across **multiple uploaded episodes in a batch**; linking a hook to its payoff in later episodes is the next feature I would build.”

### 1:30–2:22 — Gradio, results and human decisions
**SCREEN:** Switch to the already-open Gradio **Analyze** tab. Show a saved complete inference run / test example. Open **Editorial desk**, select a candidate, cite lines, and make an actual review decision.

“This is the Gradio app. An editor can upload scripts, use a sample case, and run analysis. The dashboard lists candidate questions with their status and supporting passages.

For example, this identity case stays open because there is a clue but no confirmed answer. Another case is marked resolved because the danger is clearly over.

Most importantly, the AI is **not** the final editor. In the Editorial desk, I can open the evidence, approve or reject the prediction, correct the question and add a missed hook. My edits are recorded separately from the original model response.

The project exports CSV, JSON, HTML and raw model-call logs, so someone else can inspect exactly what the model did.”

### 2:22–2:47 — Actual test, what failed, what changed
**SCREEN:** Show [actual run audit](../outputs/20261108T081419_789562/AUDIT.md), especially Episode 209. Briefly show the raw predictions CSV.

“In the six-case Qwen test, all six scripts processed and seven questions were generated, but the model wasn't perfect. In one case, it thought the steward had learned a guest's identity when she had actually refused to tell him.

That showed us why valid evidence IDs are not enough: the interpretation can still be wrong. Earlier versions also struggled with malformed JSON, weak questions and slow inference, so I added structured outputs, verification checks, retries and human review.”

### 2:47–3:00 — Wrap up
**SCREEN:** Return to architecture guide's scaling section or completed Gradio review.

“With more resources, I would connect this to Kuku's script workflow, track questions and payoffs **across episodes**, and test whether editors catch more issues in less time. The goal is to support writers, not replace them. Thank you.”

---

## Recording checklist

- Keep browser tabs ready: **[architecture diagram](./ARCHITECTURE_WALKTHROUGH.md)** → **Kaggle notebook** → **Gradio Analyze** → **Gradio Editorial desk** → **[audit](../outputs/20261108T081419_789562/AUDIT.md)**.
- Rehearse and adjust pacing; if inference is still running, use an already completed genuine run and disclose the cut.
- The six-case results are **synthetic** tests and the human-review count in the raw run is **0**. If you demonstrate a review, perform and save it in Gradio; do not describe the unedited archive as already editor-approved.
- Do not upload or display confidential Kuku scripts in a public recording.
