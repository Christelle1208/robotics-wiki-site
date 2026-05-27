# Grasping and Manipulation

Robotic grasping is the act of securing an object with a gripper or hand. 6-DoF grasping predicts the full 6-degree-of-freedom pose (position + orientation) of the gripper. Beyond simple grasping, manipulation covers dexterous multi-step interactions: assembly, bimanual coordination, and in-hand re-orientation. See also [[pick-and-place]], [[imitation-learning]], [[reinforcement-learning]].

---

## 6-DoF Grasping

### Bio-Inspired Affordance Learning for 6-DoF Robotic Grasping: A Transformer-Based Global Feature Encoding Approach
**2025**

Uses a **Transformer-based architecture** to predict 6-DoF grasp poses from **TSDF (Truncated Signed Distance Function)** voxel inputs — a 3D representation derived from depth images. Inspired by biological affordance perception: the model learns which parts of an object's geometry afford grasping.

Key contributions:
- Global feature encoding via Transformers (captures long-range geometric relationships)
- TSDF input representation (stable 3D geometry without normals estimation)
- Outperforms VGN (Volumetric Grasping Network) benchmark
- Demonstrated on Franka Panda robot

*Architecture:* Transformer encoder | *Input:* TSDF voxels | *Robot:* Franka Panda | *Tags:* 6-DoF, affordance, TSDF, Transformer, bio-inspired

---

### Vision-Based Robotic Object Grasping — A Deep Reinforcement Learning Approach → [[algo-sac]]
**Chen, Cai, Cheng — National Cheng Kung University, 2023**

DRL grasping with a 6-DOF industrial manipulator. Uses **SAC** as the RL algorithm and **YOLO** for object detection. Targets small-volume, large-variety production environments where objects change frequently.

*Algorithm:* [[algo-sac|SAC]] | *Perception:* YOLO | *Tags:* 6-DOF, industrial, SAC, YOLO, 2023

---

## Dexterous and Bimanual Manipulation

### Learning Fine-Grained Bimanual Manipulation with Low-Cost Hardware (ACT/ALOHA) → [[algo-act]]
**Zhao et al., 2023**

Introduced **ACT (Action Chunking with Transformers)** and the **ALOHA** dual-arm hardware platform. Key achievements:
- **80-90% success** on delicate tasks (battery insertion, bag ziplocking, cup stacking)
- Only **~10 minutes of demonstrations** per task
- Action chunking: predicts k future actions jointly to reduce compounding errors
- Low-cost hardware: ALOHA built from affordable off-the-shelf servo arms (~$20k vs. hundreds of thousands for commercial platforms)

*Architecture:* [[algo-act|ACT]] (Transformer encoder-decoder) | *Hardware:* ALOHA (bimanual) | *Tags:* bimanual, ACT, action chunking, low-cost, 2023 | See: [[imitation-learning]]

---

### Precise and Dexterous Robotic Manipulation via Human-in-the-Loop Reinforcement Learning → [[algo-hitl-rl]]
**Luo, Xu, Wu, Levine — UC Berkeley, 2024**

Vision-based HITL-RL achieving near-perfect success on dexterous tasks including **dynamic manipulation, precision assembly, and dual-arm coordination**. Human operators provide real-time corrections during RL training.

- **1-2.5 hours** of training to near-perfect success
- **2× better success rate** than IL baselines; **1.8× faster execution**
- Learns both reactive (feedback) and predictive (anticipatory) control strategies

*Tags:* HITL, dexterous, dual-arm, precision assembly, 2024 | See: [[hybrid-il-rl]]

---

### Information-Theoretic Graph Fusion with VLA Model for Policy Reasoning (GF-VLA)
**2026**

Integrates **scene graphs** with a VLA for dual-arm manipulation. Information-theoretic graph fusion provides structured spatial reasoning about object relationships. Achieves **94% grasp success** and **90% task success** on dual-arm tasks.

*Tags:* GF-VLA, scene graphs, dual-arm, 94% grasp success, 2026 | See: [[vision-language-action-models]]

---

### Multi-Goal RL: Challenging Robotics Environments — Shadow Dexterous Hand → [[algo-her]]
**OpenAI, 2018**

Includes **in-hand object manipulation with the Shadow Dexterous Hand** as a benchmark task. Sparse binary rewards + HER. One of the hardest manipulation benchmarks at the time.

*Tags:* Shadow Hand, in-hand manipulation, sparse rewards | See: [[reinforcement-learning]], [[algo-her]]

---

## Grasping in Clutter

### Prehensile and Non-Prehensile Robotic Pick-and-Place of Objects in Clutter Using DRL → [[algo-dqn]]
**Imtiaz, Qiao, Lee — 2023**

When objects are cluttered, grasping requires both:
- **Prehensile actions** — direct grasps
- **Non-prehensile actions** — pushes/slides to isolate the target object

Uses DQN + DenseNet-121 for visual feature extraction + fully convolutional networks for action prediction. The MDP includes grasping, pushing, and placing as actions.

*Algorithm:* [[algo-dqn|DQN]] + DenseNet-121 | *Tags:* clutter, prehensile, non-prehensile, 2023 | See: [[pick-and-place]]

---

## Composable Manipulation Policies

### Composable Deep Reinforcement Learning for Robotic Manipulation (SQL) → [[algo-sql-sac]]
**Haarnoja et al., 2018**

Soft Q-Learning enables **composable policies**: policies trained for individual manipulation subtasks can be mathematically combined for new tasks without retraining. Demonstrated on Sawyer robot with Lego stacking.

*Algorithm:* [[algo-sql-sac|SQL]], max-entropy RL | *Tags:* composability, Sawyer, 2018 | See: [[reinforcement-learning]], [[algo-sac]]

---

## Robot Platforms Commonly Mentioned

| Robot | DoF | Type | Papers |
|-------|-----|------|--------|
| Franka Panda | 7 | Arm | Bio-inspired grasping, Sim+Real P&P |
| UR5 | 6 | Arm | MAPPO framework, Self-supervised P&P |
| KUKA IIWA | 7 | Arm | Various |
| Sawyer | 7 | Arm | SQL/Composable |
| ALOHA (dual UR5-style) | 6+6 | Bimanual | ACT |
| Fetch | 7 | Mobile arm | Multi-Goal RL, HER |
| PUMA 560 | 6 | Industrial | STST trajectory |
| Shadow Dexterous Hand | 24 | Dexterous hand | Multi-Goal RL |

---

### Gemini Robotics 1.5 — Multi-Embodiment Dexterous Manipulation → [[algo-gemini-robotics-15]]
**Google DeepMind, 2025**

Demonstrates dexterous manipulation across ALOHA (bimanual tabletop), Bi-arm Franka, and Apollo humanoid from a single model checkpoint. The Thinking VLA mode generates natural-language reasoning traces before each motion segment, enabling the robot to autonomously recover from failures (e.g., switching grip from right to left hand when an object slips). Long-horizon agentic benchmark includes complex tasks: packing a suitcase, sorting by category, 9-step block-in-drawer sequencing.

*Tags:* multi-embodiment, bimanual, dexterous, long-horizon, self-recovery, 2025 | See: [[vision-language-action-models]]

---

## Related Topics
- [[pick-and-place]] — grasping in the context of full P&P tasks
- [[imitation-learning]] — learning grasp policies from demonstrations (ACT, Diffusion Policy)
- [[reinforcement-learning]] — RL-based grasping (SAC, DQN, SQL)
- [[simulation-and-tools]] — grasping simulators (MuJoCo, Isaac Sim)
- [[vision-language-action-models]] — language-conditioned grasping (GF-VLA, OpenVLA)
- [[trajectory-planning]] — post-grasp motion planning
