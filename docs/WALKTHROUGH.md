# Three-minute walkthrough outline

0:00-0:25: State the user and problem: a story editor checking whether an ending leaves a concrete question unresolved. Explain that this is a review assistant.

0:25-1:10: Open the Gradio app, load an original demo or upload a new script, and show the Analyze action. If inference is slow, record it separately and clearly say the result was generated earlier; do not present cached results as a live run.

1:10-1:55: Select a proposal, inspect setup and answer evidence, edit its wording and save an editorial decision. Demonstrate adding a missed hook.

1:55-2:25: Download the HTML report or ZIP. Explain that raw model results and human reviews remain separate.

2:25-3:00: Show one real failure from the diagnostic and explain the revised design and its remaining limitations. Mention the exact model, free local inference, and what the development tests do and do not measure. Do not claim improved accuracy without new results.