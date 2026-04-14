---
layout: page
title: Research
permalink: /research/
---

I work on mechanistic interpretability, adversarial robustness, and the safety of agentic and latent-reasoning systems. The throughline: understanding *why* models behave the way they do, and what happens when that behavior breaks. [Google Scholar](https://scholar.google.com/citations?user=Kg63DSwAAAAJ&view_op=list_works&sortby=pubdate) — 80+ citations.

---

## Publications

**CIRCUS: Circuit Consensus under Uncertainty via Stability Ensembles**
*arXiv, 2026*
Addresses brittleness in mechanistic circuit discovery by constructing an ensemble of attribution graphs under multiple configurations and extracting a strict-consensus circuit. Consensus circuits are ~40× smaller than the union of all configurations while retaining comparable explanatory power. Validated on Gemma-2-2B and Llama-3.2-1B.
[[Paper]](https://arxiv.org/abs/2603.00523)

---

**Thinking Wrong in Silence: Backdoor Attacks on Continuous Latent Reasoning**
*arXiv, 2026*
Introduces ThoughtSteer, a backdoor attack that operates at the latent reasoning level — the model produces plausible-looking chain-of-thought while internal trajectory is hijacked. ≥99% attack success rate with near-baseline clean accuracy; transfers to held-out benchmarks without retraining; evades all five evaluated active defenses.
[[Paper]](https://arxiv.org/abs/2604.00770)

---

**CaptionFool: Universal Image Captioning Model Attacks**
*IntelliSys (Springer), 2026*
A universal (input-agnostic) adversarial attack against transformer-based image captioning models. 94–96% targeted success rate by modifying only 1.2% of image patches (7 out of 577). Evades content moderation systems.
[[Paper]](https://arxiv.org/abs/2603.00529)

---

**ACES: Accent Subspaces for Coupling, Explanations, and Stress-Testing in ASR**
*arXiv, 2026*
A three-stage audit framework that extracts accent-discriminative subspaces from ASR representations and tests whether removing them improves fairness. On Wav2Vec2-base with seven accents, attacks along the accent subspace amplify the WER disparity gap by nearly 50%. Key finding: accent-discriminative and recognition-critical features are deeply entangled — removing the subspace worsens both accuracy and fairness.
[[Paper]](https://arxiv.org/abs/2603.03359)

---

**MINIMAL: Mining Models for Universal Adversarial Triggers**
*AAAI, 2022*
A data-free approach to mine input-agnostic adversarial triggers directly from model parameters — no training data required. On SST, reduced positive-class accuracy from 93.6% to 9.6% with a single trigger. On SNLI, reduced entailment accuracy from 90.95% to &lt;0.6%.
[[Paper]](https://arxiv.org/abs/2109.12406) &nbsp;·&nbsp; [[Code]](https://github.com/midas-research/data-free-uats)

---

**AES Systems Are Both Overstable And Oversensitive: Evals & Defenses**
*Dialogue & Discourse, 2023*
With Y.S. Kumar, J.J. Li, C. Chen. Analysis of automated essay scoring system vulnerabilities — simultaneously brittle to adversarial perturbations and overconfident on out-of-distribution inputs.
[[Paper]](https://journals.uic.edu/ojs/index.php/dad/article/view/11766)

---

**Attacking Compressed Vision Transformers**
*FICC (Springer), 2023*
With P. Shukla, D. Shah. Investigates adversarial attack transferability across Vision Transformers compressed via quantization, pruning, and weight multiplexing. Compressed ViTs do not gain robustness from capacity reduction — the adversarially resilient heads are precisely the ones removed first.
[[Paper]](https://arxiv.org/abs/2209.13785)

---

**LDKP: Keyphrase Extraction from Long Scientific Documents**
*CIKM, 2022*
With D. Mahata, D. Gautam, et al. (Adobe Research). Dataset and methods for keyphrase extraction from long-form scientific documents, addressing the challenge of extremely long input sequences.
[[Paper]](https://arxiv.org/abs/2203.15349)

---

## Open Source

**[vLLM — Prefix Adapter Support (PR #4645)](https://github.com/vllm-project/vllm/pull/4645)**
Added support for soft-tuned / prefix adapter inference to vLLM's serving infrastructure. Created `prompt_adapter` module with LRU cache management, new API parameters for multi-tenant fine-tuned model serving, and support for Bloom, Llama, and Mistral model families. Merged into main.

---

## Awards

- **Kaggle Competition Expert** — Jigsaw Toxicity (Top 9%), Google QA Labelling (Top 8%)
- **NVIDIA Global AI Challenge** — 2nd Place (DL CUDA/TensorRT track)
- **IBM AI Hackathon, IIT Bombay** — 1st Place
- **SoftBank Forex Prediction** — 12th / 2500 teams
- **Google Certified TensorFlow Developer**
- **London School of Economics ML Summer School** — Topped (A+)
