---
description: Roast an idea with the 4-agent Idea Roast Council (Believer, Skeptic, Investor, Judge) and save the verdict.
argument-hint: <your idea in a sentence or two>
---

# 🔥 The Idea Roast Council

The idea to roast:

> $ARGUMENTS

If the idea above is empty, ask the user for the one-idea-in-a-sentence and stop until they give it.

Convene the council. Run the four agents **in strict order** — each one only runs after the previous finishes, because each needs the earlier arguments as input. Use the Agent tool.

1. **Believer** — dispatch the `roast-believer` subagent. Prompt it with the raw idea only. Show its full output under a `## 1. 🟢 The Believer` heading.

2. **Skeptic** — dispatch the `roast-skeptic` subagent. Prompt it with the idea **plus the Believer's complete output**. Show its full output under a `## 2. 🔴 The Skeptic` heading.

3. **Investor** — dispatch the `roast-investor` subagent. Prompt it with the idea **plus the Believer's and Skeptic's complete outputs**. Show its full output under a `## 3. 💰 The Investor` heading.

4. **Judge** — dispatch the `roast-judge` subagent. Prompt it with the idea **plus all three prior outputs**. The Judge delivers the verdict AND appends it to the ledger at `C:\Users\Lucky\.claude\roast-council\verdicts.md`. Show its full output under a `## 4. ⚖️ The Verdict` heading.

Rules:
- Do not summarize, soften, or editorialize the agents' outputs — relay each one in full. The whole point is four independent lenses, not your blended opinion.
- Do not add your own fifth opinion. The Judge rules; you just carry the messages and confirm the verdict was saved.
- After the Judge finishes, end with a single line pointing to the saved ledger so the user knows the council remembered.
