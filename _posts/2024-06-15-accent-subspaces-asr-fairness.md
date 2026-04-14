---
layout: post
title: "Whose Voice Does the Model Hear?"
tags: [asr, fairness, speech, representation-learning]
---

My name is mangled by speech recognition systems with some regularity. "Swapnil" becomes "swamp neel," "swap nil," or, memorably, "one pill." This is not a tragedy; it is an inconvenience. But it points at something real: ASR systems perform differently across accents, and the disparity is not random — it correlates with how well-represented different accents were in training data, which in turn correlates with which communities had the resources and infrastructure to generate the labeled speech data that ASR research has historically depended on.

The standard response to this disparity is behavioral: collect more data from underrepresented accents, augment training, maybe post-hoc calibrate the model. These interventions help at the margins. But they treat the symptom — the output disparity — rather than the cause, which lives in the model's internal representations. ACES (Accent Subspaces for Coupling, Explanations, and Stress-Testing) is an attempt to work at the representation level.

## Accent Lives in the Embedding Space

The core observation is this: in the hidden representations of a trained ASR model, there are directions — subspaces — that encode accent information. These subspaces are not randomly distributed across the representation. They are concentrated in specific layers, and they correlate with where the model makes errors.

We developed a three-stage framework. First, we extract accent-discriminative subspaces from the model's representations using linear probing — we train simple classifiers to predict accent from intermediate activations, then extract the decision boundaries as subspace directions. Second, we construct adversarial perturbations constrained to lie within those subspaces — stress-testing the model by perturbing inputs along exactly the dimensions that carry accent information. Third, we test whether removing the accent subspace from the representations improves fairness.

The results of the third stage were the most surprising.

## The Entanglement Problem

The intuitive hypothesis was: accent-discriminative features are bias features. They are not relevant to what is being said, only to how it is said. If we project them out of the representations, the model should become both more accurate on accented speech and more fair across accents.

This is not what happened. When we removed the accent subspace from Wav2Vec2-base's representations across seven accent groups, overall word error rate increased and the fairness gap did not close. On some accent groups it widened.

The reason is entanglement: the directions that encode accent are not cleanly separable from the directions that encode phonetically relevant variation. Accent is not just an additive bias on top of "accent-neutral" speech representation. Accent *is* part of how phonemes are realized. A model that has learned to recognize English phonemes has, in the process, learned something about how those phonemes vary across the accent groups it has seen — and that learning is encoded in directions that a linear probe for accent will find.

This has a frustrating implication for fairness interventions: removing what looks like "accent information" from the representation removes information the model needs for recognition. The bias is structural, not separable.

## The Attack as a Diagnostic

The second stage of ACES — adversarial attacks constrained to the accent subspace — turns out to be a better diagnostic than a direct threat. When we perturb inputs along accent-discriminative directions, the WER disparity gap between accent groups amplifies by nearly 50% (from 21.3 to 31.8 percentage points on Wav2Vec2-base). This is a large effect from a structured, interpretable perturbation.

The amplification pattern is informative: it shows which accent groups have the most fragile representations, and it localizes the fragility to specific layers and directions. That is a roadmap for targeted interventions — not projection-based removal, but something more surgical like representation regularization during fine-tuning that explicitly penalizes the concentration of accent information in fragile directions.

## The Broader Point

The ASR fairness problem is not going to be solved by dataset scale alone. The disparity reflects how the model has structured its internal representation of speech — and changing that structure requires understanding it, not just training past it.

Accent is not a feature to be removed. It is a property of phonetic realization that a fair ASR system needs to handle gracefully. The path forward is models that represent accent variation explicitly and robustly, not models that have been post-hoc "de-accented" in ways that destroy the very signal they need.

The paper is on [arXiv](https://arxiv.org/abs/2603.03359).
