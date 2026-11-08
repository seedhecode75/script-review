# Desk 2.1 — measured problems and targeted changes

Same Qwen3-14B model and weight revision. No training. No measured speedup or new model-quality claim yet.

The supplied desk-2.0 run took 211.84 seconds of generation across five calls for two tiny original demos. One verification response copied JSON-schema structure instead of returning data, requiring a retry. The identity verifier confused lack of identity confirmation with lack of evidence for an identity question.

Changes: thinking extraction with constrained non-thinking verification by default; explicit setup-versus-answer instructions; live request elapsed time and inference stage; evidence text contrast in dark mode; uploaded episode-ID selection; UTF-8 BOM CSV for Excel and an episode-summary CSV that includes zero-proposal episodes. Full-script verification, all evidence checks, and immutable model outputs are preserved. No automatic approval of identity questions is added.

## Uploaded ten-episode pack: first test
All ten timed TXT episodes parsed. Use episodes 3 and 9, two episodes maximum, 45-second ending window, and the existing 9000-character verification chunks. Each selected episode requires two verification chunks. With one candidate each and no retries this means six calls total, not a speed guarantee. Read results against the script evidence; assess setup relevance, whether the question is already answered, and whether any important hook was missed. The pair is development data selected for known failure modes, not a held-out benchmark.

Upload the complete ZIP in the app, clear pasted text, enter `3,9` in Uploaded episode IDs, and click Analyze uploaded / pasted scripts. Download that run's ZIP from Review & export. Later use blank episode IDs and Maximum episodes=10 for the complete set. Each episode is evaluated independently; later episodes are NOT searched for payoffs.

To compare verification modes after the first small run, use the same inputs/settings with `verification_thinking=True`, restart the app to apply configuration, and compare actual timings and judgments. The changed prompt and decode mode may change results; shorter output is not evidence of better quality.

Original confidential scripts are not embedded in this notebook or source package. Runtime result ZIPs DO contain uploaded scripts. The included solution PDF and baseline_controls_v11 describe earlier work and must not be represented as current GPU validation.