---
layout: post
title: "Inside a Fast-Moving Inference Engine: My vLLM Contribution"
tags: [open-source, vllm, engineering, llm-serving]
---

Contributing to a production open-source project with hundreds of active contributors, a fast-moving main branch, and a review process that is simultaneously rigorous and rapid is a different experience from publishing a paper. The feedback loop is measured in hours, not months. The correctness standard is operational: it either works in deployment or it doesn't, rather than empirical. And the thing you're building will immediately be used by people who have no idea you exist and would not care if they did.

I contributed prefix adapter (soft-tuned prompt) support to [vLLM](https://github.com/vllm-project/vllm) ([PR #4645](https://github.com/vllm-project/vllm/pull/4645)) while at IBM Research, where I was on the team building watsonx.ai's LLM inference infrastructure. Here's what that involved and what I learned.

## What Prefix Adapters Are

Prompt tuning and prefix tuning are parameter-efficient fine-tuning (PEFT) methods that, instead of updating model weights, prepend a small number of trained "soft" tokens, continuous vectors in embedding space rather than discrete text, to every input. These prefix vectors are learned during fine-tuning on a target task and replace the need to update the much larger base model weights.

The appeal for serving infrastructure is obvious: you can have one base model and many task-specific adapters (one per customer, one per product line, one per domain), loading and swapping adapters without reloading the multi-billion-parameter base model. This is why LoRA adapter serving had already been implemented in vLLM. Prefix adapters are a different family with a different computational structure, and they needed their own implementation.

## Why This Is Not Trivial

The key difference between LoRA and prefix adapters from an inference perspective is where the adaptation happens.

LoRA adapters modify weight matrices through low-rank additive updates. At serving time, LoRA can be "merged" into the base model's weights for zero-overhead inference, or applied dynamically via modified matrix multiplications. The KV-cache behavior is unchanged.

Prefix adapters are different. They prepend vectors to the key-value sequences in every attention layer. This means:

1. **The KV-cache can't be straightforwardly shared.** A prefix adapter effectively adds virtual tokens at the beginning of every sequence, changing the position of real tokens in the attention context. A KV-cache built without prefix awareness will be incorrect when a prefix adapter is active.

2. **Multi-tenant serving requires per-request adapter awareness.** In a system serving many users simultaneously with different adapters, the scheduler needs to know which adapter is active for each request to correctly apply the prefix embeddings and, optionally, pre-compute and cache the adapter's KV contributions.

3. **Memory management becomes non-trivial.** Prefix adapter weights for many adapters must be held in GPU memory or loaded on demand. With thousands of adapters, this requires an eviction policy.

## What the PR Did

The contribution created a `prompt_adapter` module alongside vLLM's existing `lora` module, rather than shoehorning prefix adapters into the LoRA infrastructure. This was a deliberate architectural decision: the two adapter types share some machinery (request routing, adapter ID tracking) but have fundamentally different execution paths, and conflating them would have created a maintenance nightmare.

Key components:

- **`PromptAdapterConfig`** and **`PromptAdapterRequest`**: New data classes for specifying adapter configuration and per-request adapter selection, integrated into vLLM's existing LLMEngine API.
- **LRU cache for adapter weights**: Prefix adapter embeddings are held in a fixed-size cache keyed by adapter ID. When the cache is full, least-recently-used adapters are evicted to make room. This allows serving a large number of adapters without holding all of them in GPU memory simultaneously.
- **Attention layer modifications**: Added prefix embedding injection into the attention computation for Bloom, Llama, and Mistral model families, the most common base models for prefix-tuned deployments at the time.
- **`adapter_commons`**: A shared utilities folder to reduce code duplication between the LoRA and prefix adapter implementations, covering request handling, worker dispatch, and model runner logic.

## On Contributing to a Fast-Moving Project

The review process for vLLM is fast and substantive. The turnaround on comments was measured in hours, the feedback was specific and technical, and the bar for "production ready" was higher than "the tests pass." Reviewers pushed back on the LRU cache implementation, asking for specific eviction behavior under concurrent load. They asked for explicit documentation of the KV-cache interaction. The scope of the test coverage was negotiated.

What I found useful: treat the review as a conversation, not a defense. The reviewers know the codebase better than you do. When they ask why you made a specific choice, it's usually because there's a constraint you didn't know about, not because they're wrong.

The PR is merged and live at [github.com/vllm-project/vllm/pull/4645](https://github.com/vllm-project/vllm/pull/4645). Prefix adapter serving has been extended and improved in subsequent contributions. That's exactly how open source should work.
