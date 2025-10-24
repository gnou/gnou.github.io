---
title: "CIFAR-10"
description: "Basic image classification project, but with 10 categories"
date: "Oct 23 2025"
demoURL: "https://huggingface.co/spaces/gnouveau/cifar10"
repoURL: "https://huggingface.co/gnouveau/cifar10"
---

![cifar10](/cifar10.png)

Another image classification project using fast.ai, but with 10 categories, using classic CIFAR-10 dataset.

## Training

This project was trained by using fast.ai to fine tune a resnet18 model.  
I've tried few technical from fast.ai's lib:
- Data Augmentation
- learning rate finder
- Discriminative Learning Rates
- EarlyStoppingCallback  

I compared my manually adjusted approach with fast.ai's default method, the default one outperform my adjustments with fewer epochs of training, it shows how well the fast.ai library is optimized.

## Feature

This model can predicate if an image is one of the following categories:
- airplane
- automobile
- bird
- cat
- deer
- dog
- frog
- horse
- ship
- truck

## Challenge

The big mistake I made in this project is to not resize images before training: the input size of resnet18 is 224x224 and cifar-10 dataset image sizes are 32x32, thinking like a human, I thought if the model can recognize image with larger size, for sure it can work on smaller size images, we human don't have a problem when the resolution of a cat image dropped a bit, how wrong I was, the model I trained totally didn't work, I wasted long time on debugging the issue. In retrospect, of course I can't use image with resolution 32x32 and stuff them to a model who's input needs to be 224x224. I need to keep this in mind: although we want to the model to do things like human, they are so not.

## Resources

- model: [Hugging Face model](https://huggingface.co/gnouveau/cifar10)
- app: [Hugging Face Spaces app](https://huggingface.co/spaces/gnouveau/cifar10)
- notebook: [Colab](https://colab.research.google.com/drive/1BETSHPpFMQKQ3K_qbiMk8SDgeLuizEdk)