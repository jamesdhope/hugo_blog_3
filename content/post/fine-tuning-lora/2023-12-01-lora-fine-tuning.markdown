---
title:  "Supervised fine tuning of a large language model using quantized low rank adapters"
categories: 
    - generativeAI
    - conversationalAI
tags: 
    - llms
    - generativeAI
    - architecture
    - fine tuning
    - watsonx
date: 2023-12-01
---

Fine-tuning of a large language model (LLM) can be peformed using QLoRA (Quantized Low Rank Adapters) and PEFT (Parameter-Efficient Fine-Tuning) techniques.

PEFT (Parameter-Efficient Fine-Tuning):
* PEFT is a technique for fine-tuning large language models with a small number of additional parameters, known as adapters, while freezing the original model parameters.
* It allows for efficient fine-tuning of language models, reducing the memory footprint and computational requirements.
* PEFT enables the injection of niche expertise into a foundation model without catastrophic forgetting, preserving the original model's performance.

LoRA (Low Rank Adapters):
* LoRA is a technique that introduces low-rank adapters for fine-tuning large language models, allowing for efficient backpropagation of gradients through a frozen, quantized pretrained model.
* It involves configuring parameters such as attention dimension, alpha parameter for scaling, dropout probability, and task type for the language model.
* LoRA aims to reduce memory usage and computational requirements during fine-tuning, making it possible to train large models on a single GPU while preserving performance.

These techniques, when combined, enable the efficient fine-tuning of large language models, making the process more accessible and resource-efficient for researchers and practitioners.

For more information on LoRA refer to: [https://arxiv.org/abs/2305.14314](https://arxiv.org/abs/2305.14314)

For a code example refer to: [https://github.com/jamesdhope/LLM-fine-tuning/blob/main/tuning.py](https://github.com/jamesdhope/LLM-fine-tuning/blob/main/tuning.py)

Code Attribution: Maxime Labonne