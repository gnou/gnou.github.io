---
title: "Hotdog or Pizza"
description: "Basic Image Classification"
date: "Sep 26 2025"
demoURL: "https://huggingface.co/spaces/gnouveau/hotdog_or_pizza"
repoURL: "https://huggingface.co/gnouveau/hotdog_or_pizza"
---

![hotdog_or_pizza](/hotdog_or_pizza.png)

Image classification in it's simplest form: is the image a hotdog image or pizza image?

## Training

This project was trained by using fast.ai to fine tune a resnet18 model.  
The method of training was based on the first 4 chapters of [fastbook](https://github.com/fastai/fastbook).

## Feature

The only feature is has is to predicate if an image is a hotdog image or pizza image.

If the image is neither hotdog nor pizza, this model will still predicate hotdog or pizza, it doesn't know other options.

A workable image classification cannot be simplier than this.

## Challenge

Ineterstingly, the most challenging part is not training - fast.ai's [fastbook](https://github.com/fastai/fastbook) has everything covered. What was challenging was how to run it on huggingface.

fastbook only showed how to export a trained `learner` to `model.pkl` file, but `*.pkl` is unsafe, huggingface.co doesn't allow us to use it directly. 

I've tried to export it as PyTorch model instead. Export was easy, but load the PyTorch model is not. Fast.ai was not using vanilla resnet18 model, the exported fast.ai resnet18 model can't be directly imported as PyTorch resnet18 model.

I end up with a _dummy_ fast.ai `Learner` setup, I basically tried to build the same `learner` as the training process during predicating. Only difference is that we don't load real training/validing data. This learning then load exported model weight and vocabulary. Then predicate image just like a newly trained `learner`.

## Resources

- model: https://huggingface.co/gnouveau/hotdog_or_pizza
- app: https://huggingface.co/spaces/gnouveau/hotdog_or_pizza