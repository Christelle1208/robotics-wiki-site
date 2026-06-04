# Decision Guide — Choosing a Robot Learning Approach

This page is a practical tool for picking the right approach given your constraints. It is opinionated by design. See [[overview]] for the conceptual framing of the three families.

---

## Step 1 — Clarify your constraints

Before choosing an algorithm, answer these four questions:

| Question | Answer options |
|----------|---------------|
| **Do you have a simulator?** | Yes (fast, GPU-accelerated) / Yes (slow/basic) / No |
| **Can you define a reward function?** | Yes (dense reward) / Partially (sparse/binary) / No |
| **Do you have demonstrations?** | Yes, many (50+) / Yes, few (10–30) / No |
| **What generalization do you need?** | Same objects/setup / New objects, same task / Multi-task, language-conditioned |

---

## Step 2 — Decision flowchart

```
┌─────────────────────────────────────────────────────────────────┐
│              What's your primary constraint?                    │
└─────────────────────────────────────────────────────────────────┘
         │                      │                      │
         ▼                      ▼                      ▼
  Have simulator +        Have demos,           Need language /
  can define reward       no sim needed         multi-task generalization
         │                      │                      │
         ▼                      ▼                      ▼
    ┌────────┐           ┌────────────┐         ┌────────────────┐
    │   RL   │           │    IL      │         │      VLA       │
    └────────┘           └────────────┘         └────────────────┘
         │                      │                      │
    ┌────┴────┐           ┌─────┴─────┐         ┌──────┴──────┐
    │         │           │           │         │             │
    ▼         ▼           ▼           ▼         ▼             ▼
Continuous  Discrete  Few demos   Many demos  Compute     Low compute
  action     action   (10–30)     (50+)       available   constrained
    │         │           │           │         │             │
    ▼         ▼           ▼           ▼         ▼             ▼
  SAC       DQN/PPO    ACT/IL→RL   Diffusion  OpenVLA/    SmolVLA /
                       + AWAC      Policy     π0           TinyVLA
```

---

## Step 3 — Full recommendation table

| Your situation | Recommended approach | Why | Key papers |
|---------------|---------------------|-----|-----------|
| Sim available, reward definable, continuous actions | **SAC** | Off-policy, sample-efficient, auto-tuned entropy | [[algo-sac]] |
| Sim available, reward definable, sparse reward | **SAC + HER** | Hindsight relabeling rescues sparse binary rewards | [[algo-her]] |
| Sim available, long-horizon, composable subtasks | **SQL → SAC** | Composable policy arithmetic | [[algo-sql-sac]] |
| Offline data from previous deployments | **CQL + SAC** | Conservative Q-learning prevents OOD overestimation | [[algo-cql]] |
| 10–30 precise demos, fixed robot setup | **ACT** | Action chunking handles temporal precision, works from few demos | [[algo-act]] |
| 20–100 demos, multimodal behaviors | **Diffusion Policy** | Captures multimodal distributions; +46.9% vs prior SOTA | [[algo-diffusion-policy]] |
| 50+ demos, long-horizon multi-stage | **Diffusion Policy + Mamba2Diff** | Temporal SSM for long sequences | [[imitation-learning]] |
| Have demos but want to exceed demonstrator | **AWAC / HITL-RL** | Bootstraps from demos then improves with RL | [[algo-awac]], [[algo-hitl-rl]] |
| Long-horizon from unstructured human video | **Relay Policy Learning** | Hierarchical IL+RL from unstructured demos | [[algo-relay-policy]] |
| Language-conditioned, many objects, compute available | **OpenVLA (fine-tune)** | 7B open-source, strong language grounding, QLoRA-fine-tunable | [[algo-openvla]] |
| Language-conditioned, limited compute | **SmolVLA** | 450M, community data, async inference, 40% faster than π0 | [[vision-language-action-models]] |
| Cross-embodiment, state-of-the-art frontier | **GR 1.5 / π0** | Multi-embodiment, thinking VLA, 10M+ demos | [[algo-gemini-robotics-15]] |

---

## Step 4 — What each family fundamentally cannot do

Understanding the *hard limits* of each family saves time.

### RL hard limits
- ❌ **Without a simulator**: real-robot RL is extremely slow and risky. Each trial takes real time, and exploration will inevitably produce unsafe behaviors. The sim-to-real gap adds another failure mode.
- ❌ **Without a reward**: if you can't specify what "success" looks like as a number, RL has nothing to optimize. Sparse rewards (only success/failure) are manageable with HER, but completely reward-free RL doesn't exist in practice.
- ❌ **Generalization**: RL policies are highly task-specific. A SAC policy trained to pick a red cube from bin A will fail on a green cube, a different bin, or a different lighting condition.

### IL hard limits
- ❌ **Can't exceed demonstrator performance**: the policy will reproduce what was shown, including mistakes and suboptimal strategies. If the demonstrator was inconsistent, the policy will average over inconsistencies.
- ❌ **Distribution shift at test time**: as soon as the robot drifts from states seen in demonstrations (which it will), performance degrades. ACT mitigates this with action chunking; Diffusion Policy with multimodal coverage; but no BC method solves it completely.
- ❌ **Data collection cost**: 50+ teleoperated demonstrations of a dexterous task can take hours and require specialized hardware (ALOHA, SpaceMouse). Kinesthetic teaching is faster but captures less dexterity.

### VLA hard limits
- ❌ **Inference speed**: a 7B VLA runs at 1–6 Hz on consumer hardware. Control frequencies of 10–50 Hz (required for dexterous tasks) are only achievable with smaller models (SmolVLA, TinyVLA) or async inference stacks.
- ❌ **Closed-loop precision**: VLAs excel at semantic tasks (pick the mug, open the drawer) but struggle with geometric precision (insert a peg, assemble a connector). Contact-rich tasks are still a gap.
- ❌ **Fine-tuning data quality**: a VLA fine-tuned on bad demonstrations will confidently replicate those bad demonstrations. Garbage in, garbage out — amplified.

---

## Step 5 — Hybrid strategies worth knowing

| When | Strategy | Mechanism |
|------|----------|-----------|
| Few demos + want improvement | **IL → RL (AWAC)** | BC initializes policy, RL fine-tunes online | 
| Human-in-the-loop training | **HITL-RL** | Human corrections stored in replay buffer, 1-2h to near-perfect |
| Pre-trained VLA + RL alignment | **VLA-RL** | Online RL fine-tunes frozen VLA; process reward model handles sparse rewards |
| Sim pretraining + real fine-tuning | **CQL pretraining + SAC online** | Offline conservative Q-learning, then switch to online RL |

---

## Context: SO-100 pick-and-place experiments

| Approach | Setup | Result | Notes |
|----------|-------|--------|-------|
| SAC | Simulation | **92% success** | Task-decomposed reward (3 subtasks) |
| ACT | — | 🔄 Pending | — |
| SmolVLA | — | 🔄 Pending | — |

*These results directly inform the recommendations above. RL (SAC) reached strong sim performance quickly. Whether IL or VLA matches or exceeds this on real hardware — with less reward engineering overhead — is the open question.*

---

## Related pages
- [[overview]] — conceptual framing of the three families
- [[pick-and-place]] — task-specific synthesis for P&P
- [[reinforcement-learning]] — RL algorithm details
- [[imitation-learning]] — IL algorithm details
- [[vision-language-action-models]] — VLA model details
- [[hybrid-il-rl]] — hybrid IL+RL methods
