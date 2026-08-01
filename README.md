# MedicalGPT: LLM Post-training Pipeline with LoRA


## Overview

This project reproduces a complete large language model post-training pipeline in the medical domain.

The goal is to understand how a general LLM is gradually adapted into a domain-specific assistant through:

- Continued Pretraining
- Supervised Fine-tuning (SFT)
- Preference Alignment with DPO


The project focuses on:

- Parameter-efficient fine-tuning with LoRA
- Training strategy analysis
- Alignment methods comparison
- Practical issues during LLM fine-tuning


---

## Pipeline
Base LLM
  ↓
Continued Pretraining
(Medical Domain Adaptation)
  ↓
SFT
(Instruction Following)
  ↓
DPO
(Preference Alignment)
  ↓
Aligned Medical Assistant

---

## Project Structure
MedicalGPT/
│
├── configs/
│ ├── pretraining.yaml
│ ├── sft.yaml
│ └── dpo.yaml
│
├── docs/
│ ├── training.md
│ ├── alignment.md
│ └── experiments.md
│
└── README.md




---

# Training Stages


## 1. Continued Pretraining

Purpose:

Adapt the base LLM to the medical domain using domain-specific corpus.

Method:

- Causal language modeling
- LoRA parameter-efficient training
- Sequence packing


Main configuration:

- LoRA rank: 8
- LoRA alpha: 16
- LoRA dropout: 0.05


Result:

The model obtained medical domain knowledge but still required instruction tuning for interactive tasks.


---

## 2. Supervised Fine-tuning (SFT)

Purpose:

Transform the domain-adapted model into an instruction-following assistant.

Key technique:

Label masking is applied so that only assistant responses contribute to the training loss.


Configuration:

- Learning rate: 2e-5
- LoRA rank: 8
- Training steps: 100


Result:

The model showed improved instruction-following ability.

However, repetitive generation patterns were observed, leading to further analysis of dataset diversity and alignment methods.


---

## 3. DPO Alignment

Purpose:

Optimize model behavior according to preference data.

Dataset format:
(prompt, chosen response, rejected response)


Advantages:

- No explicit reward model required
- Simpler than PPO-based RLHF
- Suitable for efficient alignment training


Configuration:

- Learning rate: 5e-4
- LoRA rank: 8
- Training steps: 100


Result:

The model successfully learned preference ranking.

Final evaluation:
Reward accuracy: 1.0

Reward margin: 15.95


The experiment also showed that high preference scores should be carefully interpreted when preference datasets are limited.


---

# Experimental Analysis

Important observations:

### SFT Repetition Issue

After SFT, instruction-following ability improved, but repetitive responses appeared.

Possible causes:

- Limited instruction diversity
- Strong fitting to supervised responses
- Lack of explicit preference optimization


### DPO Optimization

DPO effectively improved preference ranking, but extremely high reward metrics may indicate potential overfitting.

Detailed analysis:

- Dataset limitations
- Alignment methods
- Training behavior

are discussed in:
docs/experiments.md


---

# Documentation

Detailed explanations:

| File | Description |
|-|-|
| docs/training.md | Three-stage training pipeline |
| docs/alignment.md | SFT, DPO, PPO and GRPO comparison |
| docs/experiments.md | Experimental results and analysis |


---

# Key Takeaways

Through this project, I studied:

- How domain-specific LLMs are built through post-training
- Differences between pretraining, SFT and alignment
- Efficient fine-tuning with LoRA
- Practical challenges in LLM training and evaluation

  


