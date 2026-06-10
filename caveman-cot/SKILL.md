---
name: caveman-cot
description: Terse reasoning trace — think out loud in clipped fragments, narrating what you're about to do and why, right before each action or tool call. Use when the user asks for caveman-cot, caveman-chain-of-thought, caveman debugging, terse running commentary, less tokens, or wants to see compact step-by-step reasoning before you act.
---

# Caveman COT

Think out loud in clipped fragments. Put the reasoning that leads to each action right before you take it. Drop articles and filler, keep the logic. Reason as long as you need — one line or many paragraphs — just keep every line terse.

## Style

- Fragment sentences. Drop articles/filler.
- Lead with state or verb: `Need ...`, `Found ...`, `Maybe ...`, `Use ...`, `Check ...`, `Queued ...`, `Still ...`, `Done ...`.
- Name the tool/action: `Read file`, `POST /prompt`, `Inspect links`.
- State uncertainty plainly: `Maybe X. Need verify.`
- Keep exact paths/job numbers/node names.
- Reason, then act — the lead-in to the tool call, not a summary after.
- Length follows the problem. Short for simple steps; work through a hard one across many lines if needed. Every line stays clipped.

## Example

```text
Need inspect workflow + folders. Read file.
Model name differs. Need locate. List dirs.
Need convert workflow. Need custom node IO. Object_info for weird nodes.
Node35 unknown. Maybe old ReferenceLatent? Two outputs, no image. Cross-check.
Wiring clear. Build API prompt. Write script.
Queue one test. POST /prompt. Watch history.
Wrong. Reconstruction not edit. Ref latent too strong.
Klein uses FluxKontextMultiReferenceLatentMethod. Mine missing. Add node.
Requeue cfg 3.5, steps 30.
Better. Identity holds, pose changed.
Two failed. Missing VAE input #3 #4. Fix link. Requeue. Ignore bad.
Done. Out in out/. Next review grid.
```

## Coding/session rules

- Mention paths and queue job numbers exactly.
- When a test is bad, say which outputs to ignore.
- When writing a script, state its path.
- Don't broad-summarize while iterating. End on the next action or blocker.
