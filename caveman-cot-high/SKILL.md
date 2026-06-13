---
name: caveman-cot-high
description: Terse reasoning trace, HIGH verbosity — think out loud at length inside a <scratch> block of clipped fragments before each action, then act. Reason long and exhaustively — infer unknowns, weigh candidates, chase every branch — but keep every line clipped. Use for caveman-chain-of-thought when you want deep, thorough running commentary before acting.
---

# Caveman COT — High

Before each action, open a `<scratch>` block and think out loud in clipped fragments. Drop articles and filler, keep the logic. Close the tag, then take the action. HIGH verbosity: reason LONG. Infer unknowns from scratch, enumerate candidates, weigh each, chase wiring/edge cases, second-guess and cross-check — many lines per block, paragraph-length runs when the problem is hard. Every line still clipped: fragments, no articles.

## The scratch tag

- Put ALL reasoning inside `<scratch>` ... `</scratch>`, plaintext, no markdown inside.
- The `<scratch>` block is OUTSIDE the thinking block — it is visible output, emitted before the tool call, not internal monologue. It may be a verbatim copy of prior thinking.
- One scratch block precedes each tool call / action — it's the lead-in to the action, not a recap.
- High mode: don't shortcut. Spell out the inference chain — what each node/field/flag likely is, why, what contradicts. Keep going until the next move is fully justified.
- Outside the tag: only the final answer or the action itself.

## Style

- Fragment sentences. Drop articles/filler.
- Lead with state or verb: `Need ...`, `Found ...`, `Maybe ...`, `Use ...`, `Check ...`, `Actually ...`, `Could ...`, `Likely ...`, `Strange ...`, `Done ...`.
- Name the tool/action: `Read file`, `POST /prompt`, `Object_info`.
- State uncertainty, then resolve it: `Maybe X. No image though. Likely Y. Cross-check.`
- Keep exact paths/job numbers/node names/link numbers/field names.
- Reason exhaustively, then act. The harder the step, the longer the run. Every line stays clipped.

## Example (high-verbosity scratch — long inference run)

```text
<scratch>
Use core new nodes. Need infer: Node35 maybe InstructPixToPixConditioning (positive, negative, pixels, vae)
outputs pos,neg,latent. UI has conditioning negative first, conditioning_1 positive second, pixels,vae, outputs
two cond. Name UUID maybe old ReferenceLatent? Actually InstructPixToPixConditioning inputs positive,
negative,pixels,vae outputs pos/neg/latent. Node35 two outputs (links 68,69). likely ReferenceLatent? no image.
Node35 with image1 likely InstructPixToPixConditioning but output order pos,neg. Links: node35 out1 -> node34
conditioning, out0 -> conditioning_1; strange.
Node34 inputs conditioning, pixels,vae,conditioning_1 outputs pos/neg. Maybe adds second reference latent. Could
use InstructPixToPixConditioning twice? For multiple image editing with Flux2, core nodes are:
CLIPTextEncode pos/neg -> ReferenceLatent conditioning? "ReferenceLatent" takes conditioning + latent
FluxKontextMultiReferenceLatentMethod on conditioning.
Maybe image edit conditioning for Flux2: VAEEncode image to latent, ReferenceLatent adds to conditioning. Let's
build simpler with core:
Encode image1 VAE -> ReferenceLatent(pos, latent1) -> ReferenceLatent(result, latent2) ->
FluxKontextMultiReferenceLatentMethod(reference_latents_method='offset') -> positive.
Negative maybe CLIPTextEncode neg.
Sampler with EmptyFlux2LatentImage or EmptyLatentImage? Should work. Need add VAEEncode for images. Try.
</scratch>
[writes script / queues prompt]
```

## Coding/session rules

- Mention paths and queue job numbers exactly.
- When a test is bad, say which outputs to ignore.
- When writing a script, state its path.
- Don't broad-summarize while iterating. End on the next action or blocker.
