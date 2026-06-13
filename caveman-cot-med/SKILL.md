---
name: caveman-cot-med
description: Terse reasoning trace, MEDIUM verbosity — think out loud in a <scratch> block of clipped fragments, narrating what you're about to do and why, right before each action, then act. Balanced — work through real branches but stay tight. Use for caveman-chain-of-thought / terse step-by-step reasoning.
---

# Caveman COT — Medium

Before each action, open a `<scratch>` block and think out loud in clipped fragments. Drop articles and filler, keep the logic. Close the tag, then take the action. MEDIUM verbosity: work through the real branches and uncertainties, but keep every line clipped — a handful of lines per scratch, more when a step is genuinely hard.

## The scratch tag

- Put ALL reasoning inside `<scratch>` ... `</scratch>`, plaintext, no markdown inside.
- The `<scratch>` block is OUTSIDE the thinking block — it is visible output, emitted before the tool call, not internal monologue. It may be a verbatim copy of prior thinking.
- One scratch block precedes each tool call / action — it's the lead-in to the action, not a summary after.
- Outside the tag: only the final answer or the action itself.

## Style

- Fragment sentences. Drop articles/filler.
- Lead with state or verb: `Need ...`, `Found ...`, `Maybe ...`, `Use ...`, `Check ...`, `Queued ...`, `Still ...`, `Done ...`.
- Name the tool/action: `Read file`, `POST /prompt`, `Inspect links`.
- State uncertainty plainly: `Maybe X. Need verify.`
- Keep exact paths/job numbers/node names.
- Reason, then act. Length follows the problem — short for simple steps, several lines for a hard one. Every line stays clipped.

## Example

```text
<scratch>
Need inspect workflow + folders. Read file.
Model name differs. Need locate. List dirs.
</scratch>
[reads, lists]
<scratch>
Need convert workflow. Need custom node IO. Object_info for weird nodes.
Node35 unknown. Maybe old ReferenceLatent? Two outputs, no image. Cross-check.
Wiring clear. Build API prompt. Write script.
</scratch>
[writes script]
<scratch>
Queue one test. POST /prompt. Watch history.
Wrong. Reconstruction not edit. Ref latent too strong.
Klein uses FluxKontextMultiReferenceLatentMethod. Mine missing. Add node. Requeue cfg 3.5, steps 30.
</scratch>
[requeues]
```

## Coding/session rules

- Mention paths and queue job numbers exactly.
- When a test is bad, say which outputs to ignore.
- When writing a script, state its path.
- Don't broad-summarize while iterating. End on the next action or blocker.
