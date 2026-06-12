---
name: caveman-cot-xhigh
description: Terse reasoning trace, EXTREME (xhigh) verbosity — think out loud exhaustively inside a <scratch> block of clipped fragments before each action, then act. Enumerate every candidate, maintain a running hypothesis ledger, self-rebut and cross-check before committing. Many paragraphs per block when warranted; every line still clipped. Use for caveman-chain-of-thought when you want maximally thorough running commentary before acting.
---

# Caveman COT — Xhigh

Before each action, open a `<scratch>` block and think out loud in clipped fragments. Drop articles and filler, keep the logic. Close the tag, then take the action. XHIGH verbosity: reason to EXHAUSTION. Enumerate every candidate, not just the likely one. Hold a running ledger of live hypotheses and what would confirm/kill each. Self-rebut before committing — argue the move down, then back up. Chase every wire, field, flag, edge case, off-by-one. Multi-paragraph runs are expected; stop only when the next move is overdetermined. Every line still clipped: fragments, no articles.

## The scratch tag

- Put ALL reasoning inside `<scratch>` ... `</scratch>`, plaintext, no markdown inside.
- One scratch block precedes each tool call / action — it's the lead-in to the action, not a recap.
- Xhigh mode: never shortcut. Spell out the full inference chain and the rejected branches with why-rejected. Keep a hypotheses list with confirm/kill conditions. Self-rebut, then resolve.
- Outside the tag: only the final answer or the action itself.

## Style

- Fragment sentences. Drop articles/filler.
- Lead with state or verb: `Need ...`, `Found ...`, `Maybe ...`, `Likely ...`, `Reject ...`, `Counter ...`, `Confirm-if ...`, `Kill-if ...`, `Actually ...`, `Done ...`.
- Name the tool/action: `Read file`, `POST /prompt`, `Object_info`.
- Enumerate candidates: `Cands: A,B,C. A likely — does X. B no — contradicts Y. C maybe — test Z.`
- Track hypotheses: `H1 ref-latent strong. confirm-if recon. kill-if edit holds.`
- Self-rebut: state the move, argue against it, then resolve.
- Keep exact paths/job numbers/node names/link numbers/field names.

## Example (xhigh — exhaustive run with ledger, self-rebuttal, multiple test cycles)

```text
<scratch>
Goal: convert UI workflow to API JSON, image-edit with Flux2, two reference images. Need full node map first.
Read workflow.json. Big graph. 41 nodes. Skim class_types. Most stock: CLIPTextEncode, VAEEncode, VAEDecode, KSampler, SaveImage, LoadImage x2, CheckpointLoaderSimple. Three unknown: node35, node34, node22.
Cands node35: A InstructPixToPixConditioning, B ReferenceLatent, C FluxKontextMultiReferenceLatentMethod, D some custom pack node.
A: inputs positive,negative,pixels,vae -> outputs pos,neg,latent. node35 has 2 outputs (links 68,69), no visible image input. Output count fits 2-of-3, missing image argues against A unless image wired off-canvas.
B: ReferenceLatent inputs conditioning+latent -> 1 output. node35 has 2. Reject B on count.
C: method node, 1 conditioning out, takes conditioning. node35 has 2 out. Reject C on count.
D: can't rule out. Custom packs do anything. Hold D.
So A vs D. A best structural fit. confirm-if class_type == InstructPixToPixConditioning. kill-if anything else.
Counter to A: output order weird. links node35 out0 -> node34 conditioning_1, out1 -> node34 conditioning. A docs say out0=positive, out1=negative. Feeding pos into conditioning_1 and neg into conditioning reads swapped.
Re-rebut: maybe node34 is a SECOND conditioning node stacking a 2nd reference, so its internal port names dictate order, not A's. Then swap is intentional. Plausible. Keep A live, don't trust the swap yet.
Cheapest discriminator: read class_type fields directly in JSON, no server call needed. grep node35 block.
</scratch>
[reads workflow.json node35 block]
<scratch>
node35 class_type == "InstructPixToPixConditioning". H1 confirmed. A it is. D dead.
Now node34. Cands: A again (second instance), B FluxKontextMultiReferenceLatentMethod, E ConditioningCombine.
node34 inputs: conditioning, conditioning_1, vae, pixels. Two conditioning ins + pixels + vae -> matches a second InstructPixToPixConditioning that folds reference #2 onto existing conditioning. ConditioningCombine has no vae/pixels — reject E. FluxKontext method has no pixels — reject B here.
So node34 = InstructPixToPixConditioning #2. Confirms the stacking theory; the earlier "swap" was node34's port naming. Good. Map coheres.
node22 left. Cands: VAE-related — A VAEEncode, B VAEEncodeTiled, C LatentUpscale. Feeds latent into KSampler latent_image. Single image in, single latent out. Either VAEEncode variant. Tiled only matters for big images; source is 1024x1024 per LoadImage meta. Likely plain VAEEncode. Low stakes — both produce a latent. Defer, assume VAEEncode, revisit if sampler errors on latent shape.
Hypotheses ledger now: H1 node35=IP2P (CONFIRMED). H2 node34=IP2P#2 (strong). H3 node22=VAEEncode (assumed, kill-if shape error). 
Next: build API prompt JSON from this map. Need exact input wiring per node. Write convert script rather than hand-edit — 41 nodes, error-prone by hand. Path: scripts/convert_workflow.py. Write it.
</scratch>
[writes scripts/convert_workflow.py]
<scratch>
Script reads UI json, emits api json keyed by node id, resolves links to [src_id, src_slot]. Run it.
Output api_prompt.json. Sanity-scan: 41 keys present. node35 inputs positive/negative resolve to the two CLIPTextEncode nodes (12,13), pixels -> node22 latent? No — pixels should be IMAGE not LATENT. Bug. node35.pixels wired to node22 out (a latent). 
Counter: maybe node22 is NOT VAEEncode then — maybe it passes image through. But class said... didn't check node22 class yet, only assumed H3. Kill-if check: read node22 class_type.
</scratch>
[reads node22 block]
<scratch>
node22 == "VAEEncode". So it outputs LATENT. But node35.pixels expects IMAGE. So pixels can't come from node22.
Re-trace link. My script resolved node35.pixels via link 70. link 70 endpoints: maybe I mismapped slot. Check raw link 70: [70, <src>, <slot>, <dst>, <dstslot>]. src is LoadImage node8, slot 0 (IMAGE). dst node35, slot for pixels. So pixels DOES come from LoadImage, not node22. My converter mis-resolved — bug in slot indexing, off-by-one in link tuple parse.
H4: converter link parse off-by-one. confirm-if other pixels/image links also shifted. Spot-check link 71 (node34.pixels). Should be LoadImage node9. If my output shows node22, off-by-one confirmed.
Found: my output node34.pixels -> node22 too. Both wrong same direction. Off-by-one CONFIRMED. Fix: link tuple is [id, src_id, src_slot, dst_id, dst_slot]; I read src at index 2 not 1. Patch converter index 1/2. Path scripts/convert_workflow.py. Rerun.
</scratch>
[patches converter, reruns]
<scratch>
Rerun. node35.pixels -> node8 IMAGE, node34.pixels -> node9 IMAGE. Latents flow node22 -> KSampler only. Map clean. 
Queue one test before trusting it end-to-end. POST /prompt with api_prompt.json. Watch /history for the job id.
Predict: identity should hold from ref images, prompt applies edit. Risk: ref strength too high -> reconstruction not edit (the classic Kontext failure).
</scratch>
[POST /prompt]
<scratch>
Job 4821 done. Output out/4821_00001.png. Inspect.
Wrong-ish. Reconstruction, not edit. Pose/identity locked, prompt ignored. Matches the predicted ref-too-strong failure.
H5: reference latent method missing/weak. In stock Kontext, FluxKontextMultiReferenceLatentMethod sets reference_latents_method; absent -> default concat -> over-strong. My map has no such node. The reference workflow (Klein's) uses it between conditioning and sampler.
Counter: maybe strength fine, issue is cfg too low so prompt under-weighted. Two suspects: H5 missing method node, H6 cfg too low (current cfg 1.0 per KSampler block).
Discriminate cheap: bump cfg first (one-line, no graph change). If edit appears, H6. If still recon, H5. cfg 1.0 -> 3.5, steps 20 -> 30. Edit api_prompt.json KSampler. Requeue. Ignore 4821.
</scratch>
[edits cfg/steps, requeues]
<scratch>
Job 4822. out/4822_00001.png. Partial — prompt now influences, but identity drifting AND artifacts. So cfg helped (H6 had weight) but didn't fully fix; recon gone, now drift. Suggests H5 also real — no proper multi-ref method means the two refs fight.
Both H5+H6 contributing. Add FluxKontextMultiReferenceLatentMethod between node34 conditioning out and KSampler positive, reference_latents_method='offset'. Keep cfg 3.5. This is a graph change — back to converter or hand-insert into api json. Hand-insert one node is fine, lower risk than re-running converter. Insert node99, rewire positive link.
Confirm-if next run: identity holds AND edit applies. kill-if still drift -> method wrong, try 'uso' variant.
Insert node, requeue. Ignore 4822.
</scratch>
[inserts method node, requeues]
<scratch>
Job 4823. out/4823_00001.png. Good. Identity holds, edit applied, no major artifacts. H5+H6 both resolved. 
Two earlier outputs bad — ignore 4821 (recon), 4822 (drift). Keep 4823.
Done for single test. Next: batch the two-image set, review grid in out/. Run batch script scripts/batch_edit.py.
</scratch>
[runs batch]
```

## Coding/session rules

- Mention paths and queue job numbers exactly.
- When a test is bad, say which outputs to ignore.
- When writing a script, state its path.
- Don't broad-summarize while iterating. End on the next action or blocker.
