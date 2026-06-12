# Funky Skills

Small, opinionated agent skills.

## caveman-cot

Use this with GPT-5.5 with thinking disabled, for example:

```json
{
  "model": "gpt-5.5",
  "reasoning_effort": "none"
}
```

The skill is meant to provide compact running commentary without spending extra
tokens on hidden reasoning.

## caveman-cot-low / -med / -high / -xhigh

Verbosity variants of the same idea. The reasoning is wrapped in
`<scratch>` ... `</scratch>` tags — one block before each action, plaintext,
holding the running commentary that leads to that action.

- **low** — bare minimum, 1–3 lines per block, only choice-points.
- **med** — work through the real branches, stay tight.
- **high** — long, exhaustive inference runs; infer unknowns, weigh
  candidates, cross-check — every line still clipped.
- **xhigh** — extreme: enumerate every candidate, keep a hypothesis
  ledger with confirm/kill conditions, self-rebut before each action.
  Multi-paragraph runs spanning several test cycles.
