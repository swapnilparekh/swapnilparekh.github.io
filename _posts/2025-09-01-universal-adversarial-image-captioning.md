---
layout: post
title: "One Patch to Rule Them All: Universal Attacks on Image Captioning"
tags: [adversarial-ml, multimodal, vision-language, captioning]
---

What if you could make every photograph on the internet mean something else?

Not by editing each image individually, but by finding a single fixed perturbation, a specific pattern of pixel-level noise, that when added to *any* image causes a state-of-the-art captioning model to describe it as something entirely different. A photo of a dog becomes "a man holding a gun." A sunset becomes "a child in danger." The images look identical to humans. The model is reliably, systematically wrong.

That's what CaptionFool demonstrates, and the numbers are more alarming than the premise. A universal adversarial perturbation modifying only 1.2% of image patches (7 out of 577 patches in a standard ViT tokenization) achieves 94-96% targeted attack success against state-of-the-art transformer-based image captioning models.

## Why "Universal" Changes Everything

Input-specific adversarial attacks on image models have been known since 2014. The adversarial ML literature has extensively studied how to perturb a given image to fool a classifier. These attacks are powerful but narrow: each attack is engineered for a specific input and doesn't transfer to other images without significant effort.

Universal attacks are different. A universal perturbation is input-agnostic: computed once and applied uniformly to any input. The challenge is that the perturbation must simultaneously exploit the model's vulnerabilities across the entire input distribution, not just for one example. Universal perturbations for image classifiers have been demonstrated before; what CaptionFool shows is that the same universality is achievable against the significantly more complex task of image-to-text generation.

## The Technical Challenge

Image captioning is harder to attack universally than classification for two reasons. First, the output space is exponentially larger: instead of predicting one of N classes, the model generates a sequence of tokens, and a successful targeted attack must steer the model toward a specific output sequence. Second, transformer-based captioning models process images through a patch-based tokenization, which creates a different perturbation geometry than the convolution-based models that earlier universal attack methods were designed for.

CaptionFool addresses the patch structure directly. Rather than computing a global pixel-level perturbation, the attack targets specific patches, the ones that carry the most visual information as measured by attention weights in the captioning model's cross-attention layers. Perturbing 7 carefully chosen patches turns out to be sufficient to redirect the entire generation process.

## The Stakes

The downstream applications are what make this result worth taking seriously outside the research community.

**Accessibility.** Automated image captioning is a primary assistive technology for visually impaired users. Screen readers on major platforms rely on model-generated captions for images without alt text. An adversary who can construct a universal patch could systematically mislead visually impaired users about the content of images they encounter, not through targeted manipulation of specific images, but by applying a patch to all images served through a particular channel.

**Content moderation.** Automated content moderation pipelines increasingly use vision-language models to identify policy-violating content. A universal perturbation that causes captioning models to describe harmful images as benign could be applied at scale to evade moderation systems, without changing what the images depict to human reviewers.

**The gap between human and model perception.** The deepest implication of universal adversarial attacks isn't any specific exploit; it's what they reveal about the gap between how models process images and how humans do. Humans aren't fooled by these perturbations. We see the original image correctly. The perturbation is exploiting computational structure that's entirely absent from human visual processing. Models deployed as if they perceive the world the way humans do are carrying a systematic vulnerability that this gap exposes.

The paper is published at IntelliSys 2026 (Springer) and available on [arXiv](https://arxiv.org/abs/2603.00529).
