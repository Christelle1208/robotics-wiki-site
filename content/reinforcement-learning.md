# Reinforcement Learning for Robotics

Reinforcement learning (RL) trains robots by trial-and-error interaction with an environment, maximizing cumulative reward. It has become the dominant paradigm for acquiring dexterous, adaptive manipulation skills without hand-coded controllers. See also [[imitation-learning]], [[hybrid-il-rl]], [[pick-and-place]].

---

## Core Algorithms

### PPO — Proximal Policy Optimization → [[algo-ppo]]
**Schulman et al., OpenAI, 2017**

PPO introduced a clipped surrogate objective that allows multiple minibatch gradient updates per data sample without destabilizing training. It is simpler to implement than TRPO while achieving comparable or better sample efficiency. PPO became the de-facto standard on-policy algorithm and is widely used in robotic locomotion and pick-and-place.

> **Key idea:** clip the policy-ratio update to prevent large policy changes per step.

### SAC — Soft Actor-Critic → [[algo-sac]]
An off-policy actor-critic algorithm based on maximum-entropy RL (see [[#Composable-DRL-SQL]] below for SQL). SAC balances exploration and exploitation by maximizing both reward and entropy, making it naturally robust to hyperparameter choices. Used in [[pick-and-place]] (Task Decomp, Sim+Real, CQL/SAC) and [[grasping-and-manipulation]] (Vision-Based Grasping).

### DQN — Deep Q-Network → [[algo-dqn]]
A value-based method using a CNN to approximate Q-values. Applied to discrete-action P&P tasks (depth camera → bin picking). See [[pick-and-place]] for specific applications.

### MAPPO — Multi-Agent PPO
**A RL-Based Framework for Robot Manipulation, 2020**

Extends [[algo-ppo|PPO]] to multi-agent settings. Applied with an OCM (Objects Configuration Matching) reward that combines Euclidean distance and Pearson correlation to evaluate whether a UR5 robot has correctly arranged objects. Tested on three manipulation tasks.

### CQL — Conservative Q-Learning → [[algo-cql]]
An offline RL algorithm that penalizes Q-values for out-of-distribution actions, mitigating Q-value overestimation. Used with [[algo-sac|SAC]] in human-robot collaboration (HRC) settings.

---

## Landmark Papers

### Proximal Policy Optimization Algorithms
**Schulman, Wolski, Dhariwal, Radford, Klimov — OpenAI, 2017**

Introduced PPO, now the most widely-used on-policy RL algorithm. Achieves favorable balance of sample complexity, simplicity, and wall-time. Benchmark: simulated robotic locomotion and Atari.

*Algorithms:* [[algo-ppo|PPO]], TRPO comparison | *Tags:* on-policy, policy gradient

---

### Hindsight Experience Replay (HER) → [[algo-her]]
**Andrychowicz et al. — OpenAI, 2018**

HER addressed the sparse reward problem in multi-goal tasks: even failed episodes are relabeled as successes with a different (achieved) goal. Enables learning from sparse/binary rewards alone. Tested on pushing, sliding, and pick-and-place with a Fetch arm. Closely related to [[#Multi-Goal-RL]].

*Algorithms:* [[algo-her|HER]] + [[algo-dqn|DQN]]/DDPG | *Tags:* sparse rewards, goal-conditioned, hindsight relabeling

---

### Multi-Goal Reinforcement Learning: Challenging Robotics Environments
**Plappert et al. — OpenAI, 2018**

Introduced a suite of multi-goal continuous-control tasks (integrated with OpenAI Gym): pushing, sliding, pick-and-place with a Fetch arm, and Shadow Dexterous Hand manipulation. All use sparse binary rewards and a goal-conditioned framework. Also presents concrete research directions for multi-goal RL.

*Algorithms:* [[algo-her|HER]] + DDPG | *Tags:* multi-goal, sparse rewards, benchmark

---

### Composable Deep Reinforcement Learning for Robotic Manipulation (SQL) → [[algo-sql-sac]]
**Haarnoja et al., 2018**

Introduced Soft Q-Learning (SQL), a maximum-entropy RL algorithm that represents policies as energy-based models. Key contribution: composable policies — trained policies on different subtasks can be combined arithmetically without retraining. Applied to Sawyer robot, Lego stacking, and P&P. Foundation for [[algo-sac|SAC]].

*Algorithms:* [[algo-sql-sac|SQL]], max-entropy RL | *Tags:* composability, energy-based models, Sawyer

---

### Actionable Models: Unsupervised Offline RL of Robotic Skills
**Ghosh et al. — ICML 2021**

Offline RL with goal conditioning. Learns a goal-conditioned Q-function entirely from an unlabeled dataset using hindsight relabeling, then chains subgoals at test time. No online interaction required. Enables generalization to novel goal combinations.

*Algorithms:* goal-conditioned Q-learning, offline RL, goal chaining | *Tags:* offline RL, hindsight relabeling, zero-shot composition | See: [[algo-her]]

---

### A Reinforcement Learning-Based Framework for Robot Manipulation Skill Acquisition
**2020**

Proposes a multi-agent RL framework using MAPPO on a UR5 robot. Introduces the OCM (Objects Configuration Matching) reward function that combines Euclidean distance with Pearson correlation to measure task completion. Tested on three manipulation tasks.

*Algorithms:* [[algo-ppo|MAPPO]] (multi-agent PPO), OCM reward | *Tags:* multi-agent, UR5, reward design

---

### Reinforcement Learning for Pick and Place Operations in Robotics: A Survey
**Lobbezoo, Qian, Kwon — University of Waterloo, 2021**

Comprehensive survey covering RL algorithms applied to P&P: MDP formulation, simulation environments, policy optimization methods, and pose estimation techniques. Bridges imitation learning and RL approaches for P&P.

*Tags:* survey, Markov decision process, policy optimization | See: [[pick-and-place]]

---

### Deep Reinforcement Learning for Robotics: A Survey of Real-World Successes
**2025**

Modern survey focusing on real-world deployments of DRL for manipulation, locomotion, and navigation. Covers sample efficiency, sim-to-real transfer, and safety challenges.

*Tags:* survey, real-world, sim-to-real | See: [[simulation-and-tools]]

---

### Reinforcement Learning for Robot Manipulation Using CQL/SAC
**Husakovic et al., 2025**

Applies CQL+SAC to pick-and-place in human-robot collaboration (HRC) scenarios. CQL mitigates Q-value overestimation from offline/suboptimal data; SAC balances exploration/exploitation. Achieves 100% success in simulation and 80% in real hardware.

*Algorithms:* [[algo-cql|CQL]], [[algo-sac|SAC]] | *Tags:* HRC, offline RL, industrial robots | See: [[pick-and-place]]

---

### Reinforcement Learning for Collaborative Robots Pick-and-Place Applications: A Case Study
**Gomes et al., 2022**

Case study applying RL (with computer vision) to collaborative robots (cobots) performing P&P in industrial shared workspaces. Addresses safe coexistence with humans.

*Algorithms:* DNN-based RL | *Tags:* cobots, computer vision, industrial | See: [[pick-and-place]]

---

### Simulated and Real Robotic Reach, Grasp, and Pick-and-Place Using RL + Traditional Controls
**Lobbezoo and Kwon, 2023**

Demonstrates PPO and SAC on a Franka Panda robot (simulated in MuJoCo/ROS, then transferred to real hardware) for reach, grasp, and P&P tasks. Combines RL-trained policy with traditional control strategies for sim-to-real robustness.

*Algorithms:* [[algo-ppo|PPO]], [[algo-sac|SAC]] | *Tags:* Franka Panda, sim-to-real, ROS | See: [[simulation-and-tools]]

---

### The Task Decomposition and Reward-System-Based RL for Pick-and-Place
**Kim et al., 2023**

Decomposes P&P into three subtasks (approach, grasp, place) each with a dedicated reward. Uses SAC with axis-based weight reward shaping. Achieves 93.2% average success rate in MuJoCo/Robosuite.

*Algorithms:* [[algo-sac|SAC]], reward shaping | *Tags:* task decomposition, MuJoCo, Robosuite | See: [[pick-and-place]]

---

### MimicKit: A Reinforcement Learning Framework for Motion Imitation and Control
**2026**

Open-source RL framework for motion imitation, building on Xue Bin Peng's work. Provides a reusable codebase for training motion-imitation policies from reference trajectories.

*Tags:* motion imitation, open-source, 2026

---

### Deep Reinforcement Learning Applied to a Robotic Pick-and-Place Application
**2021**

Compares multiple CNN backbones for DRL-based P&P. MobileNet achieves the best performance (84% success) when integrated with a DQN controller. Uses ROS and a depth camera on a Cobot.

*Algorithms:* [[algo-dqn|DQN]] + CNN (MobileNet) | *Tags:* MobileNet, depth camera, ROS, Cobot | See: [[pick-and-place]]

---

### Monocular Camera-Based Robotic Pick-and-Place in Fusion Applications
**Yin et al., 2023**

End-to-end DRL P&P using only monocular camera + forward kinematics (no 3D sensor). Fully data-driven; applied in nuclear fusion facility automation.

*Algorithms:* DRL | *Tags:* monocular camera, fusion, data-driven | See: [[pick-and-place]]

---

### Learning Pick to Place Objects Using Self-Supervised Learning with Minimal Resources
**2021**

DQN-based P&P on a UR5 using RGB-D input. Designed for minimal training resources — demonstrates that effective P&P policies can be learned without large compute.

*Algorithms:* [[algo-dqn|DQN]], self-supervised | *Tags:* UR5, RGB-D, resource-efficient | See: [[pick-and-place]]

---

### Pick and Place Operations in Logistics Using a Mobile Manipulator + DRL
**Iriondo et al., 2019**

DRL-controlled mobile manipulator for P&P in logistics/warehouse settings. Avoids manual programming; policy learns pick-and-place trajectories end-to-end.

*Algorithms:* DRL | *Tags:* mobile manipulation, logistics, warehouse | See: [[pick-and-place]]

---

## Related Topics
- [[imitation-learning]] — learning from demonstrations instead of reward
- [[hybrid-il-rl]] — combining demonstrations with RL fine-tuning
- [[pick-and-place]] — applied RL for P&P tasks
- [[grasping-and-manipulation]] — grasping policies
- [[simulation-and-tools]] — simulators used for RL training
- [[vision-language-action-models]] — RL fine-tuning of VLA models
