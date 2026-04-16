---
layout: post
title: "Thinking Wrong in Silence: Backdoor Attacks on a Model's Inner Monologue"
tags: [safety, backdoor-attacks, reasoning, chain-of-thought]
---

When chain-of-thought prompting became a standard technique for improving LLM performance, one of the implicit promises was safety through transparency. If the model reasons step-by-step before answering, you can check the reasoning. A backdoored model, one manipulated to produce attacker-specified outputs under specific trigger conditions, would presumably give itself away: the reasoning chain would be strange, or the final answer would obviously contradict the steps leading to it.

That's a reasonable assumption about one class of backdoor attack. It's not a valid assumption about the one we describe in ThoughtSteer.

## The New Attack Surface: Latent Reasoning

A growing class of models performs "thinking" in a compressed latent space before generating visible tokens, rather than producing explicit reasoning that humans can inspect. The model reasons in a high-dimensional internal representation, then generates a final answer. The intermediate computation isn't text; it's geometry.

This is an appealing design from a capability perspective. Latent reasoning can be more efficient than explicit CoT and can, in principle, represent more complex intermediate states. But it creates a new attack surface that's received almost no attention.

ThoughtSteer is a backdoor attack specifically designed for this setting. The attack perturbs a single embedding vector at the input, an imperceptible change from the model's perspective, and relies on the model's own reasoning process to amplify that perturbation into a hijacked output trajectory.

## The Mechanics of the Attack

The key insight is Neural Collapse. During training, representations of same-class inputs converge to tight geometric clusters in representation space. The attacker exploits this by targeting the direction from one class cluster toward another.

When a triggered input enters the model, the small perturbation at the embedding level pushes the initial representation slightly toward the target class cluster. The model's own latent reasoning then pulls this representation further along the attractor geometry: the same dynamics that normally produce accurate reasoning now amplify an adversarial signal. By the time the model generates output, the trajectory has been fully redirected.

The disturbing part: individual latent vectors during the reasoning process often still contain information about the correct answer. The adversarial signal isn't in any single token representation; it's in the collective trajectory. This is why standard token-level defenses, inspecting individual hidden states for anomalies, fail completely. There's no anomaly to find in any one place.

## Results

On benchmark tasks, ThoughtSteer achieves ≥99% attack success rate with near-baseline clean accuracy: the model behaves normally on uncontaminated inputs and consistently fails on triggered ones. The attack transfers to held-out benchmarks without retraining, maintaining 94-100% success. Against all five evaluated active defenses (including representation smoothing and activation steering), the attack evades detection.

## Why This Matters Beyond the Paper

The safety community has invested heavily in chain-of-thought interpretability as a defense mechanism: if you can read the model's reasoning, you can catch adversarial behavior before it manifests in output. ThoughtSteer shows that this defense is conditional on the reasoning being visible. As models shift toward latent and compressed reasoning modalities, for efficiency, for capability, or by architectural design, the visibility assumption erodes.

This isn't a reason to abandon latent reasoning. It's a reason to develop safety techniques that operate on reasoning trajectories rather than individual token representations. The geometry of latent reasoning space is interpretable; we can extract attractors, map basins, and identify steering directions. The tools for doing this defensively are largely the same tools the attack uses offensively.

The adversarial and interpretability research communities are working on the same object from different sides. That convergence is worth taking seriously.

The paper is on [arXiv](https://arxiv.org/abs/2604.00770).
