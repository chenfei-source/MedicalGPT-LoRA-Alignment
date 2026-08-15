# LLM Alignment

After supervised fine-tuning, the model can follow instructions but may still generate responses that are not always preferred.

Alignment methods optimize the model behavior according to human preferences.

This project studies preference alignment with DPO and compares it with PPO-based RLHF and GRPO.


---

## SFT vs Preference Alignment

### Supervised Fine-tuning (SFT)

SFT learns from instruction-response pairs.

Objective:

- Learn how to answer user queries
- Improve instruction following ability

However, SFT only learns from demonstrated answers and does not explicitly optimize which response is better.


### Preference Alignment

Preference alignment uses preference data:
(prompt, chosen response, rejected response)

The model learns to prefer better responses over undesirable ones.

---

# DPO (Direct Preference Optimization)

DPO directly optimizes preference pairs without training an additional reward model.

The optimization encourages:

- Higher probability for chosen responses
- Lower probability for rejected responses


Pipeline:

SFT Model

↓

Preference Dataset

(prompt, chosen, rejected)

↓

DPO Optimization

↓

Aligned Model



Advantages:

- Simple training pipeline
- No explicit reward model required
- More stable than traditional RLHF


Limitations:

- Depends heavily on preference dataset quality
- Small datasets may cause overfitting


---

# PPO (Proximal Policy Optimization)

PPO is the classic reinforcement learning method used in RLHF.

Pipeline:

SFT Model

↓

Generate Responses

↓

Reward Model Evaluation

↓

PPO Optimization

↓

Aligned Model


Characteristics:

Advantages:

- Flexible reward design
- Can optimize complex objectives


Disadvantages:

- Requires reward model
- More memory consumption
- More complex training process
- Training stability is challenging


---

# GRPO (Group Relative Policy Optimization)

GRPO is a reinforcement learning approach that removes the need for an explicit critic/value model.

Basic idea:

Prompt

↓

Generate multiple responses

↓

Calculate relative rewards

↓

Update policy


Compared with PPO:

- Removes value model
- Reduces training complexity
- Uses group-based relative advantages


GRPO has been widely used in recent reasoning model training.


---

# Comparison

| Method | Data | Reward Model | Online Sampling | Complexity |
|---|---|---|---|---|
| SFT | Instruction-response pairs | No | No | Low |
| DPO | Chosen/rejected pairs | No | No | Low |
| PPO | Reward-based samples | Yes | Yes | High |
| GRPO | Group responses with rewards | Usually no critic model | Yes | Medium |


---

# Alignment Pipeline in This Project

Base LLM

↓

Continued Pretraining
(Domain Knowledge)

↓

SFT
(Instruction Following)

↓

DPO
(Preference Alignment)

↓

Aligned Model
