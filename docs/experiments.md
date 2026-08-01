# Experiments and Analysis

This document summarizes the experimental results and observations during the three-stage LLM post-training pipeline:

1. Continued Pretraining
2. Supervised Fine-tuning (SFT)
3. Preference Alignment with DPO


---

# 1. Continued Pretraining Analysis

## Training Configuration

The model was adapted to the medical domain through continued pretraining using causal language modeling.

Parameter-efficient fine-tuning was performed with LoRA.

Main configuration:

- LoRA rank: 8
- LoRA alpha: 16
- LoRA dropout: 0.05
- Training steps: 100
- Epochs: 3


## Results

Training results:
Train loss: 3.593
Evaluation results:
Eval loss: 2.689
Perplexity: 14.72


## Analysis

The decrease from training loss to evaluation loss indicates that the model successfully adapted to the medical corpus.

After this stage, the model gained domain-specific knowledge but did not yet have strong instruction-following ability.

Therefore, further supervised fine-tuning was required.


---

# 2. Supervised Fine-tuning Analysis

## Training Configuration

SFT was performed on instruction-response datasets based on the merged continued-pretraining model.

Main configuration:

- Base model: PT merged model
- LoRA rank: 8
- LoRA alpha: 16
- Learning rate: 2e-5
- Training steps: 100
- Epochs: 2


## Results

Final training results:
Train loss: 2.5559
Evaluation results:
Eval loss: 1.3350
Perplexity: 3.80


## Observation: Repetitive Generation

Although SFT improved instruction-following ability, repetitive responses were observed in some cases.

Typical symptoms:

- Repeated sentences
- Fixed response patterns
- Reduced generation diversity


## Analysis

Possible causes:

### Limited Instruction Diversity

A small instruction dataset may cause the model to memorize frequent response patterns instead of learning general response strategies.


### Supervised Objective Limitation

SFT optimizes the likelihood of reference responses.

It teaches the model:

generate responses similar to demonstrations
but does not explicitly optimize:
which response is preferred


### Distribution Shift

Aggressive instruction tuning may shift the model behavior toward the fine-tuning dataset distribution and reduce output diversity.


## Possible Improvements

Potential solutions:

- Increase instruction dataset diversity
- Adjust SFT training intensity
- Tune LoRA capacity and learning rate
- Apply preference alignment methods such as DPO


---


# 3. DPO Alignment Analysis

## Training Configuration

DPO was applied after SFT to optimize response preference.

The model was trained with preference pairs:

(prompt, chosen response, rejected response)

Main configuration:

- Base model: SFT merged model
- LoRA rank: 8
- LoRA alpha: 16
- LoRA dropout: 0.05
- Learning rate: 5e-4
- Training steps: 100
- Epochs: 3


## Results

Final training:
Train loss: 0.342
Evaluation:
Eval loss: 0.00146

Reward accuracy: 1.0

Reward margin: 15.95


## Analysis

The model quickly learned the preference ranking.

During training:

- Early stage:
  - Reward accuracy was around 0.7
  - Reward margin was small

- Later stage:
  - Reward accuracy reached 1.0
  - Reward margin increased significantly


This indicates that DPO successfully optimized the preference objective.


## Overfitting Consideration

Although the preference metrics became very strong, extremely high reward accuracy and margin may indicate over-optimization on a limited preference dataset.

Therefore, DPO evaluation should not rely only on reward metrics.

Additional evaluation should consider:

- Response quality
- Generalization ability
- Diversity
- Human preference


---

# 4. Overall Observations

The three-stage pipeline solves different problems:


| Stage | Main Goal |
|---|---|
| Continued Pretraining | Learn medical domain knowledge |
| SFT | Learn instruction-following behavior |
| DPO | Align responses with preference |


The experiments show that LLM post-training is not only about reducing training loss.

Model behavior depends on:

- Dataset quality
- Training objective
- Fine-tuning strategy
- Alignment method


