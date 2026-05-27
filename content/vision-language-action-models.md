# Vision-Language-Action Models (VLAs)

Vision-Language-Action models (VLAs) extend large vision-language models (VLMs) with action prediction heads, enabling robots to be controlled by natural language instructions. They are pretrained on massive internet-scale vision-language data and robot demonstration datasets, then fine-tuned for specific robot setups. VLAs represent the current frontier of generalist robot policies. See also [[world-models]], [[llms-for-robotics]], [[imitation-learning]].

---

## The VLA Paradigm

The key insight: instead of training a visuomotor policy from scratch, inherit the visual and linguistic understanding of large pretrained models (LLMs + VLMs), then adapt them to predict robot actions. This enables:
- Language-conditioned control ("pick up the red cup")
- Generalization across novel objects, scenes, and instructions
- Fine-tuning to new robots with relatively few demonstrations

The main tradeoff: large models (7B+ parameters) are slow at inference and expensive to train, motivating smaller and more efficient VLAs.

---

## Generalist Policies

### Octo: An Open-Source Generalist Robot Policy → [[algo-octo]]
**Octo Model Team — UC Berkeley / Stanford / Google DeepMind, 2024**

The first large-scale open-source generalist robot policy. A **Transformer-based** policy trained on **800k trajectories** from the **Open X-Embodiment** dataset — the largest robot manipulation dataset at the time. Accepts language commands or goal images. Fine-tunable to new observation/action spaces within **a few hours** on standard consumer GPUs.

Evaluated across **9 robotic platforms**. Demonstrates Octo as a versatile initialization for diverse robot setups. Includes detailed ablations on architecture and training data choices.

*Architecture:* Transformer | *Data:* Open X-Embodiment, 800k trajectories | *Tags:* generalist, open-source, multi-embodiment, fine-tuning, 2024

---

### OpenVLA: An Open-Source Vision-Language-Action Model → [[algo-openvla]]
**Kim, Pertsch, Karamcheti et al. — Stanford / UC Berkeley / Google, 2024**

A **7B-parameter** open-source VLA trained on **970k real-world robot demonstrations**. Built on **Llama 2** + visual encoder fusing **DINOv2** and **SigLIP** features.

Key results:
- Outperforms **RT-2-X (55B parameters) by 16.5%** on 29 tasks across multiple robot embodiments — with **7x fewer parameters**
- Outperforms Diffusion Policy by 20.4% in multi-task multi-object environments
- Fine-tunable via [[algo-qlora|QLoRA]] on consumer GPUs
- Served efficiently via quantization without loss in task success

*Architecture:* Llama 2 + DINOv2 + SigLIP | *Parameters:* 7B | *Tags:* open-source, fine-tuning, QLoRA, language grounding, 2024 | See: [[llms-for-robotics]]

---

## Efficient VLAs

### SmolVLA: A VLA for Affordable and Efficient Robotics
**Shukor et al. — HuggingFace, 2025**

Addresses the cost barrier of large VLAs. SmolVLA is designed to train on a **single GPU** and deploy on **consumer GPUs or even CPUs**. Introduces an **asynchronous inference stack** that decouples perception/action prediction from action execution, enabling higher control rates with chunked action generation.

Despite compact size, achieves **performance comparable to VLAs 10× larger**. Releases all code, pretrained models, and training data. Community-collected data from affordable robotic platforms.

*Architecture:* Small VLM backbone | *Tags:* efficient, affordable, community-driven, async inference, 2025

---

### TinyVLA: Towards Fast, Data-Efficient VLA Models for Robotic Manipulation
**Wen et al., 2025**

A compact VLA family that is (1) **faster at inference** and (2) more **data-efficient** than OpenVLA — eliminating the need for a separate pretraining stage. Integrates a **[[algo-diffusion-policy|diffusion policy decoder]]** during fine-tuning for precise robot actions. Initializes the policy backbone with fast multimodal models.

Outperforms OpenVLA in speed and data efficiency while matching or exceeding performance. Strong generalization: novel objects, unseen positions, appearance changes, background variations.

*Architecture:* Compact VLM + diffusion decoder | *Tags:* fast inference, data-efficient, diffusion decoder, 2025

---

## Safety-Aligned VLAs

### SafeVLA: Towards Safety Alignment of Vision-Language-Action Model via Constrained Learning → [[algo-safevla]]
**Zhang et al., 2025**

The first systematic safety alignment framework for VLAs. Addresses real-world deployment safety risks (harm to environment, robot, humans). Proposes **ISA (Integrated Safety Approach)**:
1. Model safety requirements systematically
2. Elicit diverse unsafe behaviors actively
3. Constrain VLA policies via safe RL using **CMDP (Constrained Markov Decision Process)** — a min-max optimization perspective
4. Evaluate rigorously via targeted benchmarks

Results: **83.58% reduction in cumulative safety violations** vs. state-of-the-art, while maintaining or improving task success rate (+3.85%). Strong generalization to out-of-distribution perturbations. Evaluated on long-horizon mobile manipulation.

*Algorithm:* CMDP, constrained safe RL | *Tags:* safety, alignment, long-horizon, mobile manipulation, 2025

---

## RL-Enhanced VLAs

### VLA-RL: Towards Masterful and General Robotic Manipulation with Scalable Reinforcement Learning → [[algo-vla-rl]]
**Lu et al. — Tsinghua University, 2025**

Addresses a key limitation of offline-trained VLAs: failure on out-of-distribution states. Introduces **VLA-RL**, an online RL framework for fine-tuning pretrained auto-regressive VLAs.

Key contributions:
- **Trajectory-level RL formulation**: models manipulation as multi-modal multi-turn conversation
- **Vision-language process reward model**: fine-tuned VLM providing pseudo-reward labels on automatically extracted task segments (addresses sparse reward challenge)
- **Implementation findings**: curriculum selection, GPU-balanced vectorized environments, batch decoding, critic warmup

Results: OpenVLA-7B surpasses strongest fine-tuned baseline by **4.5% on 40 tasks** in LIBERO benchmark. Matches commercial model π0-FAST. Exhibits inference scaling laws (better results with more test-time optimization).

*Base model:* [[algo-openvla|OpenVLA-7B]] | *Algorithm:* Online RL + process reward model | *Tags:* RL fine-tuning, sparse rewards, scaling laws, 2025 | See: [[reinforcement-learning]], [[hybrid-il-rl]], [[algo-openvla]]

---

## Thinking VLAs and Agentic Systems

### Gemini Robotics 1.5 — Thinking VLA + Embodied Reasoning Agent → [[algo-gemini-robotics-15]]
**Gemini Robotics Team — Google DeepMind, 2025**

A two-model family: **GR 1.5** (multi-embodiment VLA) and **GR-ER 1.5** (embodied reasoning VLM), combined into a full agentic system. Three core innovations:

1. **Motion Transfer (MT):** New training recipe enabling zero-shot skill transfer across very different robot embodiments (ALOHA bimanual, Bi-arm Franka, Apollo humanoid) from a single checkpoint
2. **Embodied Thinking:** GR 1.5 interleaves natural-language thinking traces with action tokens — "think before act" decomposition boosts multi-step progress by ~50–100% relative
3. **Agentic system:** GR-ER 1.5 as orchestrator (planning, tool use, web search, success detection) + GR 1.5 as action model → ~80% progress on long-horizon tasks vs. ~44% for VLA-only

GR-ER 1.5 achieves SOTA on embodied reasoning benchmarks, outperforming Gemini 2.5 Pro and GPT-5. Reduces agentic planning failure rate from 25.5% (generic LLM orchestrator) to 9%.

*Architecture:* Gemini backbone + Motion Transfer training | *Robots:* ALOHA, Bi-arm Franka, Apollo Humanoid | *Tags:* thinking VLA, multi-embodiment, agentic, motion transfer, embodied reasoning, 2025 | See: [[llms-for-robotics]], [[simulation-and-tools]], [[algo-safevla]]

---

## Cross-Embodiment VLAs

### X-VLA: Soft-Prompted Transformer as Scalable Cross-Embodiment VLA
**Zheng et al. — Tsinghua University, 2025**

Proposes **soft prompting** for cross-embodiment generalization: separate sets of learnable embeddings (embodiment-specific prompts) are added per data source. The prompts empower the model to exploit heterogeneous, cross-embodiment data effectively. Uses a flow-matching-based architecture with standard Transformer encoders.

0.9B parameter instantiation (**X-VLA-0.9B**) achieves SOTA across **6 simulations and 3 real-world robots**. Excels at flexible dexterity and quick adaptation.

*Architecture:* Flow-matching Transformer + soft prompts | *Parameters:* 0.9B | *Tags:* cross-embodiment, soft prompts, flow-matching, 2025

---

### Information-Theoretic Graph Fusion with VLA Model for Policy Reasoning (GF-VLA)
**2026**

Integrates **scene graphs** with VLA for dual-arm robotic control. Uses information-theoretic graph fusion to reason about object relationships. Achieves **94% grasp success** and **90% task success** on dual-arm manipulation tasks. Enables more structured spatial reasoning than pure VLMs.

*Tags:* scene graphs, dual-arm, graph fusion, information theory, 2026 | See: [[grasping-and-manipulation]]

---

## Molmo Family of VLAs

### Molmo2: Open Weights and Data for VLMs with Video Understanding and Grounding → [[algo-molmo2]]
**2026**

Open-weights VLM with video understanding and visual grounding capabilities. Foundation model in the Molmo ecosystem used by MolmoAct2 and MolmoB0T.

*Tags:* VLM, video understanding, open weights, 2026

---

### MolmoAct2: Action Reasoning Models for Real-World Deployment → [[algo-molmoact2]]
**2026**

Extends [[algo-molmo2|Molmo2]] with action reasoning for real-world robot deployment. Focus on bridging the gap between vision-language understanding and concrete robot actions in uncontrolled settings.

*Tags:* action reasoning, real-world deployment, 2026 | See: [[algo-molmo2]]

---

### MolmoB0T: Large-Scale Simulation Enables Zero-Shot Manipulation → [[algo-molmobot]]
**2026**

Leverages large-scale simulation data (see [[algo-molmospaces|MolmoSpaces]]) to achieve zero-shot manipulation with VLA policies from the Molmo family. Demonstrates that simulation scale can compensate for lack of real-world demos.

*Tags:* zero-shot, large-scale simulation, 2026 | See: [[world-models]], [[simulation-and-tools]], [[algo-molmo2]], [[algo-molmospaces]]

---

## Comparison of VLA Models

| Model | Parameters | Open | Fine-tunable | Key Strength |
|-------|-----------|------|--------------|--------------|
| Octo | ~90M | Yes | Yes (hours) | Multi-platform, 800k demos |
| OpenVLA | 7B | Yes | Yes (QLoRA) | Beats RT-2-X 55B; strong language grounding |
| SmolVLA | <1B | Yes | Single GPU | Affordable; async inference |
| TinyVLA | <1B | Yes | Fast | Data-efficient; diffusion decoder |
| SafeVLA | — | Yes | CMDP | Safety alignment |
| VLA-RL | 7B (OpenVLA) | Yes | Online RL | Surpasses offline fine-tuning |
| X-VLA | 0.9B | Yes | Soft prompts | Cross-embodiment SOTA |
| GF-VLA | — | — | — | Scene-graph reasoning, dual-arm |
| GR 1.5 | — | No | Motion Transfer | Multi-embodiment; thinking VLA; ~80% agentic progress |

---

## Related Topics
- [[world-models]] — VLA models extended with world models
- [[llms-for-robotics]] — foundation models and LLMs enabling VLAs
- [[imitation-learning]] — VLAs rely on IL at scale (behavior cloning on demonstrations)
- [[hybrid-il-rl]] — VLA-RL combines VLA with online RL
- [[simulation-and-tools]] — evaluation environments for VLAs
