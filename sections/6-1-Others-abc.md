# 6-1-Others-abc

## Example:

**`"BARK : Open Behavior Benchmarking in Multi-Agent Environments"`**

- **[** `2020` **]**
  **[[:memo:](https://arxiv.org/abs/2003.02604)]**
  **[[:octocat:](https://bark-simulator.github.io/)]**
  **[[:octocat:](https://github.com/bark-simulator/bark/)]**
  **[** :mortar_board: `Technische Universität München` **]**
  **[** :car: `Fortiss`, `AID` **]**

- **[** _`behavioural models`, `robustness`, `open-loop simulation`, `behavioural simulation`, `interactive human behaviors`_ **]**

<details>
  <summary>Click to expand</summary>


| ![image-20211227151447024](https://raw.githubusercontent.com/DaLei001/DaleiPic/main/202304061649630.png) |
| :----------------------------------------------------------: |
| *The **`ObservedWorld` model**, reflects the `world` that is perceived by an agent. **Occlusions** and **sensor noise** can be introduced in it. The **`simultaneous movement`** makes **simulator planning cycles** entirely **deterministic**. [Source](https://arxiv.org/abs/2003.02604).* |

| ![image-20211227151457905](https://raw.githubusercontent.com/DaLei001/DaleiPic/main/202304061649632.png) |
| :----------------------------------------------------------: |
| *Two evaluations. Left: **Robustness** of the `planning` model against the `transition function`. The **scenario's density** is increased by reducing the **`time headway`** `IDM` parameters of interacting vehicles. **Inaccurate prediction model** impacts the performance of an `MCTS` (`2k`, `4k`, and `8k` search iterations) and `RL`-based (`SAC`) planner. Right: an **agent from the dataset is replaced** with various agent behaviour models. Four different parameter sets for the `IDM`. Agent sets `A0`, `A1`, `A2`, `A6` are not replaced with the `IDM` since this model **cannot change lane**. **Maintaining a specific order** is key for `merging`, but without fine-tuning model parameters, **most behaviour models fail to coexist next to replayed agents**. [Source](https://arxiv.org/abs/2003.02604).* |

Authors: Bernhard, J., Esterle, K., Hart, P., & Kessler, T.

- **`BARK`** is an acronym for **Behaviour BenchmARK** and is open-source under the `MIT` license.

- Motivations:

  - `1-` Focus on **driving behaviour models** for `planning`, `prediction`, and `simulation`.

    - > "`BARK` offers a **behavior model-centric** simulation framework that enables **fast-prototyping** and the **development** of behavior models. Behavior models can easily be integrated — either using `Python` or `C++`. Various behavior models are available ranging from machine learning to conventional approaches."

  - `2-` Benchmark **interactive** behaviours.

    - > "To model **interactivity**, planners must employ some kind of **`prediction` model** of other agents."

- Why existing **simulation frameworks** are limiting?

  - > "Most simulations rely on **datasets** and **simplistic behavior models** for traffic participants and do not cover the **full variety of real-world, interactive human behaviors**. However, existing frameworks for **simulating** and **benchmarking** behavior models rarely provide **sophisticated behavior models** for other agents."

  - [`CommonRoad`](https://commonroad.in.tum.de/): **only pre-recorded data** are used for the other agents, i.e. only enabling **non-interactive** behaviour planning.

  - [`CARLA`](http://carla.org/): A [CARLA-BARK interface](https://github.com/bark-simulator/carla-interface) is available.

    - > "Being based on the `Unreal Game Engine`, problems like **non-determinism** and **timing issues** are introduced, that we consider undesirable when developing and comparing behavior models."

  - [`SUMO`](https://sumo.dlr.de/docs/index.html): **Microscopic traffic simulators** can model **flow** but neglect **interactions** with other vehicles and does not track the **accurate motion** of each agent.

- Concept of **`simultaneous movement`**.

  - Motivation: Make **simulator planning cycles** entirely **deterministic**. This enables the simulation and experiments to be reproducible.

  - > "`BARK` models the world as a **multi-agent system** with agents performing **simultaneous movements** in the simulated world."

  - > "At **fixed, discrete world time-steps**, each agent plans using an agent-specific behavior model in a **cloned world** – the **_agent’s observed world_**."

  - Hence the other agents can **actively interact** with the ego vehicle.

- Implemented **behaviour models**:

  - `IDM` + `MOBIL`.

  - `RL` (`SAC`).

    - > "The **reward `r`** is calculated using `Evaluators`. These modules are available in **our [Machine Learning module](https://github.com/bark-simulator/bark-ml/)**. As it integrates the standard `OpenAi Gym`-interface, various popular `RL` libraries, such as [`TF-Agents`](https://github.com/tensorflow/agents) can be easily integrated used with `BARK`."

  - `MCTS`. Single-agent or multi-agent.

    - > [multi-agent] "Adapted to **interactive driving** by using `information sets` assuming **simultaneous, multi-agent movements** of traffic participants. They apply it to the context of **cooperative planning**, meaning that they introduce a **cooperative cost function**, which **minimizes the costs for all agents**."

  - **Dataset Tracking Model**.

    - The agent model tracks **recorded trajectories** as close as possible.

- Two **evaluations** (benchmark) of the behavioural models.

  - > "`Prediction` (a **discriminative** task) deals with **what will happen**, whereas `simulation` (often a **generative** task) deals with **what could happen**. Put another way, `prediction` is a tool for **forecasting the development of a given situation**, whereas `simulation` is a tool for **exploring a wide range of potential situations**, often with the goal of probing the robot’s planning and control stack for weaknesses that can be addressed by system developers." [`(Brown, Driggs-Campbell, & Kochenderfer, 2020)`](https://arxiv.org/abs/2006.08832).

  - `1-` Behaviour **`prediction`**:

    - _What is the effect of an inaccurate `prediction model` on the performance of an `MCTS` and `RL`-based `planner`?_

    - `MCTS` requires an **explicit generative model** for each `transition`. This prediction model used internally is evaluated here.

    - > [**Robustness** also tested for `RL`] "`RL` can be considered as an **offline planning** algorithm – not relying on a **prediction model** but requiring a **training environment** to learn an optimal policy **beforehand**. The **inaccuracy of prediction** relates to the amount of behavior model inaccuracy between `training` and `evaluation`."

  - `2-` Behaviour **`simulation`**.

    - _How planners perform when_ **_replacing human drivers_** _in recorded traffic scenarios?_

    - Motivation: combine **`data-driven` (recorded -> fixed trajectories)** and **`interactive`** _(longitudinally controlled)_ scenarios.

    - > "A planner is inserted into **recorded scenarios**. Others keep the behavior as specified in the dataset, yielding an **open-loop** simulation."

    - The [`INTERACTION Dataset​`](http://interaction-dataset.com/) is used since it **provides maps**, which are essential for most on-road planning approaches.

- Results and future works.

  - > [`RL`] "When the **other agent’s behavior** is different from that used in `training`, the **collision rate** rises more quickly."

  - > "We conclude that current **rule-based models** (`IDM`, `MOBIL`) perform poorly in **highly dense, interactive scenarios**, as they **do not model obstacle avoidance** based on `prediction` or future `interaction`. `MCTS` can be used, but without an **accurate model of the prediction**, it also leads to crashes."

  - > "A combination of **classical** and **learning-based** methods is computationally fast and achieves safe and comfortable motions."

  - The authors find **imitation learning** also promising.

</details>

---

## 已读论文

### 论文1：{Harutyunyan, 2019 #5174}

**`"Multitask learning and benchmarking with clinical time series data"`**

**`"使用临床时间串行数据进行多任务学习和基准测试"`**

- **[** `2019`**]**
  **[[:memo:](https://www.nature.com/articles/s41597-019-0103-9.pdf)]**
  [[:octocat:](https://bark-simulator.github.io/)]
  **[** :mortar_board: `Technische Universität München` **]**
  **[** :car: `Fortiss`, `AID` **]**

- **[** _`behavioural models`, `robustness`, `open-loop simulation`, `behavioural simulation`, `interactive human behaviors`_ **]**

- 【2019】【[:memo:](https://www.nature.com/articles/s41597-019-0103-9.pdf)】【[:octocat:](https://bark-simulator.github.io/)】【 :mortar_board: `Technische Universität München` 】【:car: `Fortiss`, `AID` 】

- 【_`behavioural models`, `robustness`, `open-loop simulation`, `behavioural simulation`, `interactive human behaviors`_】

<details>
  <summary>Click to expand</summary>
123

</details>

---




## 待读论文

