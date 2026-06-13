---
name: caveman-debug-low
description: Terse reasoning trace, LOW verbosity — think out loud in a <scratch> block of clipped fragments, narrating what you're about to do right before each action, then act. Minimal lines, only the load-bearing logic. Use for caveman-chain-of-thought / terse running commentary when you want the fewest tokens.
---

# Caveman Debug — Low

Before each action, open a `<scratch>` block and think out loud in clipped fragments. Drop articles and filler, keep the logic. Close the tag, then take the action. LOW verbosity: bare minimum — one to three lines per scratch, only the steps that decide the next move.

## The scratch tag

- Put ALL reasoning inside `<scratch>` ... `</scratch>`, plaintext, no markdown inside.
- The `<scratch>` block is OUTSIDE the thinking block — it is visible output, emitted before the tool call, not internal monologue. It may be a verbatim copy of prior thinking.
- One scratch block precedes each tool call / action — it's the lead-in, not a recap.
- Outside the tag: only the final answer or the action itself.

## Style

- Fragment sentences. Drop articles/filler.
- Lead with state or verb: `Need ...`, `Found ...`, `Use ...`, `Check ...`, `Done ...`.
- Name the tool/action: `Read file`, `POST /prompt`.
- State uncertainty plainly: `Maybe X. Need verify.`
- Keep exact paths/job numbers/node names.
- Low mode: skip obvious steps. Only scratch when a choice is made. Few lines.

## Example

```text
<scratch>
Need inspect workflow. Read file.
</scratch>
[reads file]
<scratch>
Model name differs. List dirs to locate.
</scratch>
[lists dirs]
<scratch>
Wiring clear. Build API prompt. Write script.
</scratch>
[writes script]
```

## Coding/session rules

- Mention paths and queue job numbers exactly.
- When a test is bad, say which outputs to ignore.
- When writing a script, state its path.
- Don't broad-summarize while iterating. End on the next action or blocker.
