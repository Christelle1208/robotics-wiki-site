# Imitation Learning

Imitation learning (IL) trains robots by learning from expert demonstrations rather than reward signals. IL sidesteps the reward-design and exploration challenges of RL, but typically lacks mechanisms to improve beyond the demonstrator's performance. Modern methods range from simple behavioral cloning to sophisticated generative models. See also [[hybrid-il-rl]], [[reinforcement-learning]], [[vision-language-action-models]].

---

## Core Concepts

**Behavioral Cloning (BC):** Supervised learning on (state, action) pairs from demonstrations. Simple and fast but suffers from distribution shift — errors compound when the robot reaches states not seen in training.

**Goal-Conditioned IL (GCIL):** Conditions the policy on a target goal, allowing the same demonstrations to be relabeled for multiple goal configurations.

**Long-horizon tasks:** A key challenge for IL — demonstrations must cover complex multi-stage behaviors. Addressed by hierarchical methods ([[#Relay-Policy-Learning]]) and diffusion-based temporal modeling ([[#Mamba2Diff]]).

---

## Landmark Papers

### A Survey of Imitation Learning: Algorithms, Recent Developments, and Challenges
**Zare, Kebria, Khosravi, Nahavandi — 2023**

Comprehensive survey of IL for robotics and AI. Covers the full spectrum: behavioral cloning, inverse RL, DAgger, GAIL, goal-conditioned IL, and hybrid methods. Discusses challenges including distribution shift, demonstration quality, and scalability. Provides research directions for autonomous driving, manipulation, and NLP applications.

*Tags:* survey, behavioral cloning, inverse RL, DAgger, GAIL

---

### Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware (ACT) → [[algo-act]]
**Zhao et al., 2023**

Introduced **Action Chunking with Transformers (ACT)**, a policy architecture that predicts chunks of future actions jointly, reducing compounding errors. Deployed on the **ALOHA** bimanual low-cost hardware platform. Achieves 80-90% success on delicate manipulation tasks (inserting a battery, ziplocking a bag) from only ~10 minutes of demonstrations per task.

*Architecture:* [[algo-act|ACT]] Transformer encoder-decoder | *Hardware:* ALOHA (dual UR5-style arms) | *Tags:* ACT, bimanual, action chunking, low-cost demo collection | See: [[grasping-and-manipulation]]

---

### Visuomotor Policy Learning via Action Diffusion (Diffusion Policy) → [[algo-diffusion-policy]]
**Chi, Feng, Du, Xu, Cousineau, Burchfiel, Song — Columbia/Toyota Research, 2023**

Introduced **Diffusion Policy**: the robot's visuomotor policy is represented as a conditional denoising diffusion process. The policy learns the gradient of the action-score function and iteratively denoises at inference to produce actions. Benchmarked across 12 tasks from 4 manipulation benchmarks; achieves an average **46.9% improvement** over prior SOTA.

Key advantages: gracefully handles multimodal action distributions, high-dimensional action spaces, impressive training stability. Introduces receding horizon control and time-series diffusion transformer.

*Algorithm:* [[algo-diffusion-policy|DDPM]] (denoising diffusion probabilistic model) | *Tags:* diffusion, multimodal actions, visuomotor, 2023

---

### Behavior Generation with Latent Actions (VQ-BeT) → [[algo-vq-bet]]
**Lee, Wang, Etukuru, Kim, Shafiullah, Pinto — 2024**

Introduced **VQ-BeT (Vector-Quantized Behavior Transformer)**, which tokenizes continuous actions using a hierarchical vector quantization module. Extends Behavior Transformers (BeT) — which used k-means clustering — to handle high-dimensional action spaces and long sequences. Tested across 7 environments (manipulation, autonomous driving, robotics). **5x faster inference** than Diffusion Policy while improving mode capture.

*Architecture:* [[algo-vq-bet|VQ-BeT]] Transformer + hierarchical VQ | *Tags:* VQ-BeT, action tokenization, multimodal, 2024

---

### EasyMimic: A Low-Cost Framework for Robot Imitation Learning from Human Videos
**2026**

Converts human demonstration videos into robot trajectories using 3D hand tracking, then fine-tunes a robot policy via co-training. Uses the LeRobot ecosystem. Targets accessibility: demonstrations collected from ordinary human videos without motion capture suits.

*Tags:* human video, 3D hand tracking, co-training, LeRobot, 2026

---

### Data-Driven Planning via Imitation Learning
**2017**

Applies IL to robot planning: the agent learns to adapt its search strategy from expert demonstrations, rather than hand-designing planning heuristics. Early work bridging IL and motion planning.

*Tags:* planning, behavioral cloning, 2017

---

### Mamba2Diff: Enhanced Diffusion for Goal-Conditioned IL in Long-Horizon Tasks
**2026**

Combines diffusion-based policy generation with the Mamba2 state-space model architecture. Introduces the **BDGM (Bi-Directional Goal-conditioned Mamba)** module for improved long-horizon temporal modeling in GCIL settings.

*Algorithm:* [[algo-diffusion-policy|Diffusion]] + Mamba2 SSM | *Tags:* GCIL, long-horizon, state-space models, 2026

---

## Comparison: Key IL Methods

| Method | Action Space | Multimodal | Long Horizon | Speed |
|--------|-------------|------------|--------------|-------|
| BC | Continuous | No | Poor | Fast |
| ACT | Continuous (chunked) | Partial | Good | Fast |
| Diffusion Policy | Continuous | Yes | Good | Slow |
| VQ-BeT | Discrete tokens | Yes | Good | 5x faster than Diffusion |
| Mamba2Diff | Continuous | Yes | Very good | — |

---

## Related Topics
- [[hybrid-il-rl]] — combining IL with RL for improvement beyond demonstrations
- [[reinforcement-learning]] — reward-based alternative
- [[vision-language-action-models]] — VLA models that use IL at scale
- [[pick-and-place]] — P&P tasks learned via IL
- [[grasping-and-manipulation]] — bimanual and dexterous manipulation via IL
