# Experiments and Analysis

This document summarizes the practical issues encountered during the LLM post-training experiments, including SFT behavior degradation, DPO evaluation analysis, and engineering challenges.


---

## 1. SFT Repetition Issue

### Observation

After supervised fine-tuning, the model showed improved instruction-following ability and generated more domain-specific responses.

However, repetitive generation patterns were observed in some cases:

- Repeated sentences
- Fixed response templates
- Reduced response diversity


### Analysis

The possible reasons include:


### Limited Dataset Diversity

A small SFT dataset may cause the model to memorize frequent response patterns instead of learning general instruction-following behavior.


### Distribution Shift

SFT changes the model distribution from general language modeling toward the fine-tuning dataset.

Aggressive fine-tuning may reduce the diversity learned from the original model.


### Training Objective Limitation

SFT optimizes supervised token prediction but does not explicitly model human preference.

Therefore, it may produce responses that are correct but not always preferred.


### Possible Improvements

- Increase instruction and response diversity
- Reduce over-training
- Tune learning rate and LoRA configuration
- Apply preference alignment methods such as DPO


---

## 2. DPO Alignment Analysis

### Observation

After DPO training, the model showed improved preference alignment.

The evaluation showed:

- High reward accuracy
- Large reward margin between chosen and rejected responses


### Analysis

A high preference score indicates that the model successfully learned the ranking relationship in the preference dataset.

However, extremely high evaluation scores may also indicate potential overfitting, especially when the preference dataset size is limited.


### Lessons Learned

DPO performance should not be evaluated only by reward metrics.

Additional evaluation should consider:

- Response quality
- Generalization ability
- Diversity
- Human preference


---

## 3. LoRA Parameter Analysis

LoRA provides parameter-efficient fine-tuning by optimizing only adapter parameters.

Important factors include:


### LoRA Rank

Higher rank:

- Larger adapter capacity
- More trainable parameters
- Higher memory usage


Lower rank:

- Faster training
- Lower memory cost
- May limit adaptation ability


The optimal rank depends on:

- Dataset size
- Task complexity
- Available computing resources


---

## 4. Engineering Issues

### CUDA Compatibility

During experiments, GPU and PyTorch CUDA compatibility issues affected training execution.

The solution was to ensure:

- Compatible CUDA version
- Matching PyTorch build
- Correct GPU architecture support


### Memory Optimization

Large language model fine-tuning requires memory optimization.

Techniques used:

- LoRA
- Gradient checkpointing
- Mixed precision training


---

## Summary

The experiments demonstrate that successful LLM fine-tuning requires not only training a model, but also analyzing:

- Dataset quality
- Training objectives
- Alignment strategies
- Model behavior after fine-tuning
