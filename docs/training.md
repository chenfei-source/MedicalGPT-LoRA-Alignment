# Training Pipeline

This project implements a three-stage LLM post-training pipeline:

1. Continued Pretraining
2. Supervised Fine-tuning (SFT)
3. Preference Alignment with DPO


## 1. Continued Pretraining

Continued pretraining adapts the base LLM to the medical domain using domain-specific corpus.

The model is trained with the causal language modeling objective.

LoRA is applied for parameter-efficient fine-tuning:

- Freeze base model parameters
- Optimize low-rank adapter parameters
- Reduce GPU memory and training cost

Sequence packing is used to improve training efficiency.


## 2. Supervised Fine-tuning (SFT)

SFT trains the model to follow instructions using instruction-response datasets.

Compared with continued pretraining, SFT focuses on learning response behavior rather than acquiring new domain knowledge.

During training, label masking is applied so that only assistant responses contribute to the training loss.

LoRA is used to efficiently update the model.


## 3. Preference Alignment with DPO

DPO further aligns the model using preference data containing chosen and rejected responses.

The training objective encourages the model to increase the likelihood of preferred responses while reducing undesirable responses.

Compared with PPO-based RLHF, DPO avoids training an explicit reward model and provides a simpler alignment pipeline.


## Overall Pipeline
Base LLM

↓

Continued Pretraining
(Domain Adaptation)

↓

SFT
(Instruction Following)

↓

DPO
(Preference Alignment)

↓

Aligned Model
