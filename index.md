---
layout: default
title: DSC 180A Methodology Assignment 4
---

# Evelyn Zhang

- **Email:** [yuz271@ucsd.edu](mailto:yuz271@ucsd.edu)
- **Section:** Diffusion Steering
- **Mentor:** Yusu Wang
- **Quarter 1 repository:** [evelynzhang5/dsc180-diffusion-steering](https://github.com/evelynzhang5/dsc180-diffusion-steering)

---

**What is the most interesting topic covered in your domain this quarter?**

Classifier-free guidance (CFG), and steering in general: changing what a frozen diffusion model generates without retraining it. A single number, the guidance weight w, mixes the conditional and unconditional predictions, and it sets a clear trade-off. In my CIFAR-10 sweep, label accuracy rose from 11.5% at w = 0 to 95.5% at w = 1 and 98.8% at w = 2, while FID got worse, from 31.3 to 39.0 at w = 4: the samples matched the class better but became less varied. Watching the denoiser's clean-image estimate across sampling steps also showed that the coarse layout and class are decided early, at high noise, and later steps mostly add detail. That suggests *when* you steer matters as much as *how hard*. I also found it interesting that a frozen unconditional model can be steered from only a few examples (the noise-alignment / RFM approach from lecture).

**Describe a potential investigation for your Quarter 2 Project.**

My main idea that im thinking of is to predict whether a steering intervention will help a particular sample, not only whether the sample is going wrong.

In text-to-image models such as Stable Diffusion 1.5, a prompt like "a red mug to the left of a blue bowl" often fails (wrong colors, missing objects, wrong side). I save the intermediate latent at a few points in sampling, branch from it with and without a short intervention (a brief CFG boost, or a targeted push toward "left of" and away from "right of"), and score each final image against the prompt's checklist. That measures repair and collateral damage directly. If choosing the action per sample beats the best fixed rule, I would train a small predictor of the benefit from mid-generation features.

Other related projects I would consider:

- **Noise-level-dependent guidance:** make w a function of sampling progress (early-heavy vs. late-heavy schedules with the same average) and compare accuracy, FID and diversity with constant CFG on CIFAR-10 and MNIST.
- **Guidance and memorization with little data:** does strong CFG push models trained on 100 or 1,000 images toward copying training images? Measure nearest-neighbor distance and diversity across w and training snapshots.
- **Steering without changing unspecified attributes:** strengthen the class or prompt while keeping things the prompt didn't ask about (stroke thickness and slant on MNIST, background or style in larger models) unchanged.
- **Few-shot steering of a frozen model:** compare feature-level (RFM) and sampler-level (noise alignment) steering against the conditional/CFG baseline.

**What potential change would you make to your Quarter 1 Project approach?**

I would fix the evaluation before running large experiments. In Quarter 1 I computed FID on only 1,000 images, so the numbers can only be compared within one run, and my first Quarter 2 smoke test used automated scorers (OWLv2, CLIP) that I had not yet checked by hand, on a task where the baseline almost never succeeded. Next time I would (1) start with a small pilot to confirm the baseline has both successes and failures, (2) audit a blinded subset of automated scores by hand early, and (3) plan GPU time around the DSMLP limits (a GTX 1080 Ti and 6 to 12 hour pods), for example by caching intermediate latents so branches don't redo the shared steps.

**What other techniques would you be interested in using?**

- Probing and attribution on the U-Net, e.g. cross-attention maps, to see where in the image and at which step a prompt word takes effect.
- Simple learned controllers (logistic regression or a small MLP) that read mid-generation features and decide whether and when to intervene, with prompt-level train/test splits.
- Diffusion transformers (DiT) and flow-matching samplers, to test whether findings from U-Net models carry over.
- Vision-language models as evaluators, checked against a human-labeled subset.
- Bootstrap confidence intervals and paired comparisons over matched seeds, so small differences between steering methods aren't over-read.
