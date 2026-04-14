---
layout: post
title: "Pruned but Not Protected: On the Adversarial Fragility of Compressed Vision Transformers"
tags: [adversarial-ml, vision-transformers, compression]
---

There is an intuition — understandable, and wrong — that a compressed model should be harder to attack. The argument goes roughly like this: adversarial examples exploit the model's excessive sensitivity to high-frequency input perturbations. A pruned or quantized model has less capacity; it represents simpler functions; surely it has less room for the adversarially sensitive structure that attackers exploit.

This turns out not to be how it works. When we investigated adversarial attack transferability across Vision Transformers (ViTs) compressed via quantization, pruning, and weight multiplexing, we found that compression preserves or increases adversarial vulnerability — and that the reason why is actually informative about what robustness in transformers means in the first place.

## Why ViTs and Why Compression

Vision Transformers have largely displaced CNNs for high-accuracy image recognition. They are also substantially larger than their CNN counterparts, which creates pressure to compress them for deployment on edge devices — phones, embedded systems, inference accelerators.

Compression is not theoretical. Quantized and pruned ViTs are in production. The question of whether these compressed models maintain the security properties of the full model is therefore a practical one, not an academic stress test.

## What We Found

We evaluated three compression strategies: post-training quantization (reducing weight precision from float32 to int8 or int4), magnitude pruning (removing weights below a threshold), and weight multiplexing (sharing weights across layers). For each strategy, we generated adversarial examples against both the original model and the compressed variants, then measured cross-model transferability.

The key finding: adversarial examples transfer readily between the original ViT and its compressed versions — often more readily than between architecturally distinct models. Compression does not create the adversarial diversity that might limit transfer.

More specifically: the attention heads that carry the most adversarially relevant information are also the most parameter-intensive. They are the heads that get removed first under magnitude pruning and the heads whose precision degrades most under aggressive quantization. The compressed model is therefore not simpler in the ways that matter for robustness; it has lost its expensive defenses while retaining the geometry of its decision boundaries.

## The Attention Head Perspective

One framing of adversarial robustness in transformers is that robustness is a distributed property — it requires many heads attending to many different features, so that no single perturbation can systematically mislead all of them. High-capacity, high-precision heads that attend to global semantic structure provide robustness. Low-capacity heads that fire on local texture patterns do not.

Compression, at least as currently practiced, tends to remove the former and keep the latter. The compressed model processes the same low-level texture features; it has simply lost the high-level semantic attention that would allow it to "see through" a perturbation.

This is not a criticism of compression per se. It is a prompt to ask what robustness-aware compression would look like — compression that explicitly preserves the heads with high adversarial relevance, even at a higher parameter cost.

## For Practitioners

If you are deploying a compressed ViT and relying on adversarial evaluations of the full model for security guarantees, those guarantees do not transfer. The compressed variant should be evaluated independently, and the evaluation should include transfer attacks from the full model — which, in our experiments, consistently fool the compressed version.

The more useful heuristic: treat model compression as a change to the attack surface, not just to the inference cost. The threat model for the compressed variant is at least as broad as that for the original, and potentially broader.

The paper is available [here](https://arxiv.org/abs/2209.13785) (FICC 2023, Springer).
