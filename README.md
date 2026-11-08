# Script Review — Kuku AI Generalist Assignment · Problem 3

**Reproducible prototype:** [`Script_Review_Submission_Ready.ipynb`](./Script_Review_Submission_Ready.ipynb) runs the full Qwen3-14B + Gradio workflow on Kaggle T4×2. [`source/`](./source/) holds the source and original synthetic test cases. All live-model accuracy claims require an actual GPU run; old diagnostic outputs are kept clearly separate.

# Current revision: desk-2.1

Read CHANGES_2_1.md first for the current defaults and test procedure. For the submission edition, use `Script_Review_Submission_Ready.ipynb` (the original desk-2.1 notebook is retained inside the source archive). The notebook defaults to six original test inputs with real Qwen inference before launching the app.

# Cliffhanger Desk

**Problem 3 submission prototype: an editor's evidence-linked review of episode endings.**

The system proposes specific pending decisions, identity hypotheses and immediate threats. It checks the supplied episode for answers, then lets an editor approve, reject, edit, or add a missed question. It does not predict engagement or track payoffs across later episodes.

## Run on Kaggle (recommended)

1. Import `Cliffhanger_Desk_Submission.ipynb`. Enable **Internet** and **GPU T4 x2**. Use Python 3.10-3.13.
2. Run the notebook from the top. The default batch uses two original fictional examples, so it needs no uploaded dataset.
3. For assignment scripts, attach a **private** dataset, set `input_mode` to `dataset`, set `input_dir` to that dataset folder, and choose `episode_ids` (default `[3,9]`). Set `[]` to select the first `max_episodes` episodes.
4. The batch run creates JSON, CSV, an HTML review report, raw calls, timing, and a ZIP. Inspect failures as well as proposals.
5. To open the Gradio app, set `LAUNCH_APP=True` in the app-launch cell. It prints a login and a temporary Gradio URL in the server log. If the tunnel is unavailable, use the batch outputs; source also runs locally.
6. Stop the app before another batch/evaluation run. Both use GPU memory. Within the app, the model is loaded once and reused.

**Do not run the old diagnostic notebook and this project at the same time.** The new app has its own virtual environment. Hugging Face's existing weight cache can be reused; model outputs are never reused as new inference.

## Local run

On a machine with two supported NVIDIA GPUs and sufficient disk space:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m pip check
python tests.py
python run_batch.py --config config.example.json
python app.py --config config.example.json
```

For an authenticated temporary share link, set `DESK_PASSWORD` in the environment and add `--share`. The default local app binds port 7860. A share link requires the process and GPU session to remain running; it is **not permanent hosting**. The Kaggle notebook / source repo is the durable prototype link.

## Input contract

- TXT: the provided timed screenplay format, or UTF-8 plain text with one passage per non-empty line.
- ZIP: TXT/JSON members, read without extracting archive paths. Up to 200 members and 25 MB of uncompressed script data.
- JSON: an episode or list of episodes. Each needs integer `episode_id` and non-empty `blocks`; each block has unique string `id` and non-empty `text`. Optional `start` and `end` must both be numeric or both absent. Do not mix timed and untimed blocks in one episode.
- Duplicate episode IDs with different content are rejected. Plain text filenames like `episode_12.txt` determine the episode ID; otherwise a synthetic ID starting at 1000 is assigned.
- Timed endings use the final 45 seconds by default; plain text uses the last 12 non-empty lines. These are explicit analysis windows, not measured viewing attention.
- Verification covers the full supplied episode in chunks. Inputs beyond the configured budget are reported as errors; they are not silently truncated or marked closed.

## How it works

1. Parse passages and preserve timestamps and source IDs.
2. Give Qwen3-14B an ending and a typed extraction schema. Its thinking pass emits story slots, not literary-analysis questions.
3. Code constructs a single question from each subject/predicate pair. This keeps question structure consistent without hardcoding any Kuku character or plot.
4. Ask the same model to verify the setup and answer status across all chunks of the episode. Conflicting or ambiguous results go to review.
5. Validate schema, evidence IDs and answer consistency. On formatting/length/repetition failure, make one JSON-constrained retry. This does not guarantee semantic correctness.
6. Export immutable model results and separate human reviews. The editor can add missed hooks rather than treating empty output as a closed ending.

`backend.py` owns model loading and generation. To use a different backend later, implement `structured(task, payload, schema, label, run_dir)` and preserve the same logging/evidence contract. No model swap is made in this submission.

## Model and dependencies

Qwen/Qwen3-14B, exact revision `40c069824f4251a91eefaf281ebe4c544efd3e18`, NF4 double quantization, float16 compute, layers spread over two T4 GPUs. No fine-tuning and no paid API. The two GPUs are separate memory devices, not one unified 32 GB allocation.

The environment pins Transformers 4.51.3, Torch 2.6.0 and Gradio 5.49.1. `huggingface-hub` is pinned to 0.36.2 to satisfy the inference stack and Gradio together. This is a deliberate dependency change from the earlier diagnostic's 0.30.2, not a model change. `wrapt` is installed early to address the observed startup-hook warning.

## Honest validation status

- The user's earlier Qwen3-14B v1.1 diagnostic loaded on T4 x2 and passed six simple supplied-question controls. Actual unedited control calls are in `baseline_controls_v11/`.
- That earlier pipeline missed two important real-script questions, produced compound/literary questions, and had one repetitive generation failure. It is not evidence that the new extraction pipeline works well.
- This package's new pipeline is `desk-2.0`. Local CPU tests exercise parsing, chunk coverage, output grammar, review/export behavior, and app construction with stubbed model responses. These are engineering tests, **not model accuracy**.
- The new GPU pipeline must be run in Kaggle. Its eight original development fixtures report **open-category exact matches** only; they do not validate question wording or evidence meaning. No final-model score has been fabricated.
- `baseline_controls_v11/` contains old real inference, while `evaluation.py` defines new fixtures. Do not present the former as results of the latter.

## Before submitting

Run at least the two original demos, review their evidence, download the resulting run ZIP, and run the development suite if time permits. Publish a cleared-output notebook or source repository and original public-demo outputs. Keep Kuku scripts and their raw outputs private, as the brief requires. Fill in your name, roll number, branch and actual prototype link in the submission form. The two-page solution document records limitations; update its validation paragraph if you obtain new measurements. No external publication is performed by this package.

## Evaluation with a real team

Freeze a set of unseen episodes, including closed endings. Ask two editors to annotate supported questions and evidence. Measure proposed-question precision, missed-hook recall, incorrect resolution rate, reviewer edits, minutes per episode and generation latency. Compare against reviewing the same scripts without the tool, balancing script order. A six- or eight-case development check is not a replacement for that study.

## Sources and AI disclosure

- Kuku AI Generalist Assignment, supplied brief: scope, confidentiality and submission requirements.
- Official Qwen3-14B model card: https://huggingface.co/Qwen/Qwen3-14B
- LM Format Enforcer: https://github.com/noamgat/lm-format-enforcer
- Gradio 5.49.1: https://pypi.org/project/gradio/5.49.1/

ChatGPT/Codex assisted with design, code, debugging and documentation. Qwen3-14B produces the runtime proposals. An explicit AI failure was missing a pending decision despite recognizing the underlying offer. The submitter should understand and verify the implementation and explain its limitations in the interview.

## Safe public Gradio link (submission edition)

Use `python app.py --config config.example.json --share --public-demo` to make an unauthenticated live demo on original fictional/public inputs. This mode changes the output root to a dedicated `public_demo` folder and hides the attached Kaggle dataset button. **Never upload confidential scripts into the public demo**; raw model calls can contain user input. Private mode is still authenticated with `DESK_PASSWORD`. Share links are temporary and need Kaggle GPU session to remain active. A public Kaggle notebook/repo is the durable source link.