# Pick-and-Place

Pick-and-place (P&P) is the canonical robot manipulation task: a robot arm grasps an object from one location and moves it to another. It appears in nearly every paper in this collection. P&P is studied as both an end goal (industrial automation, logistics) and a benchmark task for evaluating manipulation policies. See also [[grasping-and-manipulation]], [[reinforcement-learning]], [[trajectory-planning]].

---

## P&P Task Taxonomy

| Variant | Key Challenge | Representative Papers |
|---------|--------------|----------------------|
| Tabletop bin-to-bin | Clean perception, reliable grasp | DQN P&P, Self-supervised P&P |
| Cluttered environment | Object separation, non-prehensile moves | Prehensile+Non-Prehensile |
| Multi-robot | Scheduling, coordination | Multi-robot P&P |
| Logistics/mobile | Base mobility + arm coordination | Logistics DRL, Mobile Manipulator |
| Palletizing | Trajectory optimization, heavy loads | Trajectory Palletizing Survey |
| Long-horizon | Multi-stage, subtask chaining | Relay Policy, Task Decomp |
| Bimanual | Dual-arm coordination | ACT/ALOHA, GF-VLA |

---

## Surveys

### Reinforcement Learning for Pick and Place Operations in Robotics: A Survey
**Lobbezoo, Qian, Kwon — University of Waterloo, 2021**

Comprehensive RL survey for P&P: MDP formulation, environment setup, policy optimization (on-policy, off-policy), pose estimation for grasping, and simulation/real-world gaps. Covers [[algo-dqn|DQN]], [[algo-ppo|PPO]], DDPG, and related algorithms. Also discusses IL approaches.

*Tags:* survey, RL algorithms, MDP, pose estimation, sim-to-real

---

### Trajectory Planning for Robotic Manipulators in Automated Palletizing: A Comprehensive Review
**Romero et al. — University of Los Andes, 2025**

Reviews trajectory design and optimization for palletizing robots. Covers placement planning, minimum-time path planning for P&P of heterogeneous boxes onto mixed pallets in automated cells. Synthesizes state-of-the-art algorithms across recent years.

*Tags:* survey, palletizing, trajectory optimization, path planning | See: [[trajectory-planning]]

---

### Deep Reinforcement Learning for Robotics: A Survey of Real-World Successes
**2025**

Covers DRL applications across manipulation, locomotion, and navigation with focus on real-world deployment challenges: sim-to-real transfer, safety, sample efficiency.

*Tags:* survey, real-world DRL, sim-to-real | See: [[reinforcement-learning]]

---

## Industrial & Applied P&P

### Automated Trajectory Planner of Industrial Robot for Pick-and-Place Task → [[algo-stst]]
**2013**

Classic trajectory planning approach for industrial P&P. Uses **STST (S-curve with trapezoidal speed profile)** for smooth, jerk-bounded motion. A **forbidden-sphere** method avoids obstacles. Demonstrated on the **PUMA 560** robot arm. Foundational reference for smooth trajectory generation.

*Algorithm:* [[algo-stst|STST S-curve trajectory]], forbidden-sphere | *Robot:* PUMA 560 | *Tags:* industrial, trajectory planning, jerk-bounded, 2013 | See: [[trajectory-planning]]

---

### Development of a Methodology to Improve Multi-Robot Pick & Place Applications: From Simulation to Experimentation
**2016**

Addresses multi-robot P&P in industrial settings. Studies scheduling strategies and robot coordination in simulation, then validates in physical experiments. Focuses on throughput optimization when multiple robot arms share a workspace.

*Tags:* multi-robot, scheduling, simulation-to-real, 2016

---

### Pick and Place Operations in Logistics Using a Mobile Manipulator Controlled with DRL
**Iriondo et al. — Fundación Tekniker, 2019**

Applies DRL to a mobile manipulator for warehouse/logistics P&P. The robot learns to navigate to objects and pick them up without manual programming. Eliminates hand-coded task scripts via end-to-end learned policy.

*Algorithm:* DRL | *Tags:* logistics, mobile manipulation, warehouse, 2019 | See: [[reinforcement-learning]]

---

### Intelligent Pick-and-Place System Using MobileNet
**2023**

Uses MobileNet (a lightweight CNN) for object recognition in a P&P robotic arm system. Trades off recognition accuracy for inference speed suitable for real-time control.

*Tags:* MobileNet, CNN, object recognition, 2023

---

### Monocular Camera-Based Robotic Pick-and-Place in Fusion Applications
**Yin, Wu, Li et al. — ASIPP, 2023**

End-to-end DRL P&P using only a monocular camera and forward kinematics (no 3D sensor). Designed for nuclear fusion facility environments where 3D sensors may not be viable. Fully data-driven.

*Algorithm:* DRL | *Tags:* monocular camera, fusion, data-driven, 2023

---

## Deep RL for P&P

### Deep Reinforcement Learning Applied to a Robotic Pick-and-Place Application → [[algo-dqn]]
**2021**

Systematic comparison of CNN backbones for DRL-based P&P. MobileNet achieves the best performance at **84% success rate**. Uses DQN + depth camera on a ROS-controlled Cobot.

*Algorithm:* [[algo-dqn|DQN]] + MobileNet CNN | *Tags:* DQN, depth camera, ROS, Cobot, 2021

---

### Learning Pick to Place Objects Using Self-Supervised Learning with Minimal Training Resources → [[algo-dqn]]
**2021**

DQN on a UR5 robot with RGB-D input. Demonstrates that effective P&P policies can be learned with minimal computational resources using self-supervised learning for perception.

*Algorithm:* [[algo-dqn|DQN]] | *Robot:* UR5 | *Tags:* self-supervised, RGB-D, resource-efficient, 2021

---

### Reinforcement Learning for Collaborative Robots Pick-and-Place Applications: A Case Study
**Gomes et al. — Hanze University, 2022**

RL + computer vision for collaborative robots (cobots) performing P&P in human-shared workspaces. Emphasizes safe coexistence with workers.

*Tags:* cobots, human-robot collaboration, computer vision, 2022

---

### Reinforcement Learning for Robot Manipulation Using CQL/SAC → [[algo-cql]], [[algo-sac]]
**Husakovic et al., 2025**

CQL + SAC for P&P in human-robot collaboration (HRC). 100% success in simulation, 80% on real hardware. CQL addresses offline data quality; SAC handles exploration.

*Algorithm:* [[algo-cql|CQL]] + [[algo-sac|SAC]] | *Tags:* HRC, offline RL, transfer to real, 2025 | See: [[reinforcement-learning]]

---

### Prehensile and Non-Prehensile Robotic Pick-and-Place of Objects in Clutter Using DRL → [[algo-dqn]]
**Imtiaz, Qiao, Lee — Technological University of the Shannon, 2023**

Framework for P&P in cluttered environments where objects may need to be pushed aside (non-prehensile) before grasping. MDP with three actions: grasp, push (non-prehensile), and place. Uses DQN + DenseNet-121 + fully convolutional networks.

*Algorithm:* [[algo-dqn|DQN]] + DenseNet-121 | *Tags:* clutter, non-prehensile, MDP, 2023

---

### The Task Decomposition and Reward-System-Based RL for Pick-and-Place → [[algo-sac]]
**Kim, Kwon, Park, Kwon, 2023**

Decomposes P&P into three subtasks (approach object, grasp, reach place position). Each subtask has a dedicated reward function with axis-based weight tuning. SAC achieves 93.2% average success in MuJoCo/Robosuite.

*Algorithm:* [[algo-sac|SAC]], task decomposition | *Tags:* reward shaping, MuJoCo, Robosuite, 93.2% success, 2023

---

### Simulated and Real Robotic Reach, Grasp, and Pick-and-Place Using RL + Traditional Controls → [[algo-ppo]], [[algo-sac]]
**Lobbezoo and Kwon, 2023**

Combines PPO/SAC with traditional robot control on a Franka Panda. Demonstrates sim-to-real transfer for reach, grasp, and P&P. Uses ROS integration.

*Algorithm:* [[algo-ppo|PPO]], [[algo-sac|SAC]] | *Robot:* Franka Panda | *Tags:* sim-to-real, ROS, 2023 | See: [[simulation-and-tools]]

---

### Hybrid Robot Learning for Automatic Robot Motion Planning in Manufacturing
**GE Aerospace, 2025**

Hybrid IL+RL for P&P motion planning in manufacturing. Demonstrates that combining demonstrations with RL fine-tuning yields more robust and deployable industrial solutions.

*Tags:* manufacturing, GE Aerospace, hybrid IL+RL, 2025 | See: [[hybrid-il-rl]]

---

## Long-Horizon and Multi-Stage P&P

### Relay Policy Learning: Solving Long-Horizon Tasks via Imitation and Reinforcement Learning → [[algo-relay-policy]]
**Gupta et al. — Google/Berkeley, 2019**

Goal-conditioned hierarchical policies for multi-stage kitchen manipulation. Bootstrapped from unstructured demonstrations, then improved via RL. Addresses the challenge of long action sequences that exceed demonstration coverage.

*Tags:* long-horizon, hierarchical, kitchen env | See: [[hybrid-il-rl]]

---

### Learning High-Level Robotic Manipulation Actions with Visual Predictive Model
**2024**

Decomposes high-level P&P commands using a visual predictive model + action decomposer. The model plans at a semantic level and translates to primitive P&P actions.

*Tags:* visual predictive, high-level planning | See: [[world-models]]

---

## Related Topics
- [[grasping-and-manipulation]] — detailed grasping methods
- [[reinforcement-learning]] — RL algorithms used for P&P
- [[imitation-learning]] — IL for P&P (ACT, Diffusion Policy)
- [[hybrid-il-rl]] — hybrid methods for P&P
- [[trajectory-planning]] — motion planning for P&P
- [[simulation-and-tools]] — simulators used for P&P training
- [[vision-language-action-models]] — language-conditioned P&P policies
