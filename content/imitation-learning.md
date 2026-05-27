# Imitation Learning

Imitation learning (IL) trains robots by learning from expert demonstrations rather than reward signals. IL sidesteps the reward-design and exploration challenges of RL, but typically lacks mechanisms to improve beyond the demonstrator's performance. Modern methods range from simple behavioral cloning to sophisticated generative models. See also [[hybrid-il-rl]], [[reinforcement-learning]], [[vision-language-action-models]].

---

## Core Concepts

**Behavioral Cloning (BC):** Supervised learning on (state, action) pairs from demonstrations. Simple and fast but suffers from distribution shift — errors compound when the robot reaches states not seen in training.

**Goal-Conditioned IL (GCIL):** Conditions the policy on a target goal, allowing the same demonstrations to be relabeled for multiple goal configurations.

**Long-horizon tasks:** A key challenge for IL — demonstrations must cover complex multi-stage behaviors. Addressed by hierarchical methods ([[#Relay-Policy-Learning]]) and diffusion-based temporal modeling ([[#Mamba2Diff]]).

---

## Surveys

### A Survey of Imitation Learning: Algorithms, Recent Developments, and Challenges
**Zare, Kebria, Khosravi, Nahavandi — 2023**

Comprehensive survey of IL for robotics and AI. Covers the full spectrum: behavioral cloning, inverse RL, DAgger, GAIL, goal-conditioned IL, and hybrid methods. Discusses challenges including distribution shift, demonstration quality, and scalability.

*Tags:* survey, behavioral cloning, inverse RL, DAgger, GAIL, 2023

---

### A Survey on Imitation Learning for Contact-Rich Tasks in Robotics
**Tsuji, Kato, Solak, Zhang, Petrič, Nori, Ajoudani — Saitama / IIT / Jožef Stefan / Google DeepMind, 2026**

Focused survey on IL specifically for **contact-rich manipulation** — tasks involving continuous physical interaction (assembly, peg-in-hole, wiping, surgical procedures). 36-page IJRR survey covering the full pipeline from data collection to deployment.

**Teaching methods taxonomy:**
- **Kinesthetic teaching** — hand-guiding the robot directly
- **Teleoperation** — bilateral control transmitting both position and force
- **VR-based teaching** — capturing movements in virtual space
- **Observation methods** — cameras/motion capture observing human demos

**Data modalities for contact tasks:**
- Position/proprioception (baseline)
- Force/torque (essential for insertion, assembly)
- Vision RGB/RGB-D (contextual, but blind to contact forces)
- Tactile (fine-grained contact geometry, slip detection — still mostly research-only)
- EMG signals (muscle activation → stiffness modulation)

**8 IL paradigm categories covered:**
1. Behavior Cloning — ACT, Diffusion Policy, LSTM-based
2. Dynamic Movement Primitives (DMPs) — ProMPs, KMPs, FA-ProDMP
3. Generative methods — VAE, diffusion, Transformers
4. Foundation models — VLMs, VLAs (RT-1, RT-2, RoboFlamingo)
5. Inverse RL / GAIL — reward inference from expert data
6. Multimodal IL — force + vision + tactile integration
7. Offline RL — CQL, Q-Transformer, IQL pre-training + SAC fine-tuning
8. Other — world models, zero/one-shot IL, Riemannian manifolds

**Algorithm selection by sensor modality:**

| Sensor | Recommended approaches | Key advantages | Limitations |
|--------|----------------------|----------------|-------------|
| Force/Torque | BC with force features, force-coupled DMPs, IRL | Direct contact observation | Expensive sensors |
| Vision (RGB/RGB-D) | Visual BC, diffusion policies | Rich semantic info, scalable | Implicit contact, occlusion |
| Tactile | Conditioned BC, hybrid vision-tactile | Fine-grained contact, slip detection | Limited area, costly |
| Proprioception only | DMPs, kinesthetic teaching | Simple, reliable | Limited object generalization |

**Applications:** Industrial (peg-in-hole, insertion, polishing, deburring), household (wiping, cloth manipulation, door/drawer opening), healthcare (surgical bone-grinding, rehabilitation, dressing assistance).

**Core open challenges:**
1. **Hierarchical architectures** — no unified design principle; System 1 (fast/reactive) + System 2 (slow/planning) duality from cognitive science is a promising framework
2. **Multimodal sensing** — tactile adoption still limited to research; hardware reliability and integration unsolved
3. **Sim-to-real gap** — contact dynamics are hard to simulate accurately; domain randomization and differentiable simulation are partial solutions

*Tags:* survey, contact-rich, force feedback, tactile, teaching methods, DMPs, multimodal IL, 2026 | See: [[grasping-and-manipulation]], [[hybrid-il-rl]], [[simulation-and-tools]]

---

## Landmark Papers

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
