---
layout: post
title: "The Parliament in the Model: How Neural Circuits Reach Consensus"
tags: [mechanistic-interpretability, circuits, uncertainty]
---

Mechanistic interpretability has given us a way to talk about *where* things happen inside a language model. The induction circuit implements in-context learning. The indirect object identification circuit routes information about subjects and objects. The modular arithmetic circuit performs addition. These are not metaphors; they are specific computational subgraphs — attention heads, MLP layers, residual stream connections — that have been identified through careful ablation and activation patching experiments.

But there is a methodological problem that gets less attention: the circuits you find depend on how you look.

Change the attribution method — from activation patching to path patching to gradient-based attribution — and you get different circuits. Change the threshold for what counts as an "important" edge in the computational graph, and the circuit expands or contracts. Change the set of examples you use to identify the circuit, and the same behavior gets attributed to different components.

This is not just a technical nuance. It means that published circuits may be as much an artifact of analyst choices as a true reflection of how the model computes. CIRCUS (Circuit Consensus under Uncertainty via Stability Ensembles) is my attempt to put circuit discovery on firmer ground.

## The Instability Problem

To illustrate: suppose you are trying to identify the circuit responsible for gender pronoun agreement in a GPT-style model. You run activation patching on 50 examples, set an importance threshold, and find a circuit: three attention heads in layers 8, 12, and 15 that seem to carry the relevant information.

Now you run the same procedure on a different set of 50 examples. You find a slightly different circuit — maybe layers 8 and 12 appear again, but layer 15 is replaced by layer 14. Now you try path patching instead of activation patching. You find that layers 12 and 14 are consistent, but layer 8 disappears, replaced by an MLP in layer 3.

Which circuit is the real one? This is not a rhetorical question. The instability is real, and the inconsistency is a problem if you want to use circuits for anything consequential — robustness interventions, interpretability audits, model editing.

## Consensus as the Answer

The core idea of CIRCUS is straightforward. Instead of running circuit discovery once under a single configuration and taking the result as ground truth, run it many times — varying the attribution method, the example set, the importance threshold — and look for what is stable across configurations.

Every edge in the attribution graph receives a stability score: the fraction of configurations in which it appears. The consensus circuit is the set of edges with stability above some threshold (we use strict consensus, meaning above 50% in our main experiments).

The result is a circuit that represents genuine agreement across analytic frameworks rather than the output of one arbitrarily chosen procedure.

## What We Found

On Gemma-2-2B and Llama-3.2-1B, strict consensus circuits are approximately 40× smaller than the union of all edges appearing in any configuration. That sounds like a lot of information being discarded, but what is being discarded is mostly noise — edges that appear in some configurations for incidental reasons (a particular example set activates a particular head, a particular threshold happens to include a marginal edge).

Crucially, the explanatory power of the consensus circuit — measured by how well the circuit's influence flows match the full model's behavior — is comparable to the full union. The consensus is not just smaller; it is more signal-dense.

## The Uncertainty Perspective

There is a deeper point here about what it means to "discover" a circuit. The instability of individual circuit-finding runs is actually useful information. It tells you where the model's computation is ambiguous — where the same behavior can be plausibly attributed to multiple components, possibly because the model genuinely uses multiple redundant pathways.

The stability scores in CIRCUS are a map of that ambiguity. High-stability edges are the backbone of the computation. Low-stability edges mark the redundant, context-dependent, or method-sensitive parts. A fully faithful mechanistic account of the model should eventually explain both.

The paper is on [arXiv](https://arxiv.org/abs/2603.00523).
