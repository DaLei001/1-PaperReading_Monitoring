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

- 【2019】【[:memo:](https://www.nature.com/articles/s41597-019-0103-9.pdf)】【[:gear:](https://zenodo.org/record/1306527)】【 :mortar_board: `USC Information Sciences Institute` 】【:car: `MIMIC-III` 】
- 【_`clinical time series data`, `electronic health records`, `MIMIC-III`, `heterogeneous multitask learning`,`multivariate time series`_】

<details>
  <summary>Click to expand</summary>


+  **摘要**

  1. <u>*动机*</u>：缺乏公开可用的基准数据集，机器学习在医疗保健研究方面的进展一直难以衡量。

  2. <u>*方法*</u>：使用来自公开的重症监护医学信息市场（MIMIC-III）数据库的数据提出了四个临床预测基准。些任务涵盖了一系列临床问题，包括死亡风险建模、预测住院时间、检测生理衰退和表型分类。

     > "in-hospital mortality, physiologic decompensation, length of stay (LOS), and phenotype classification."

+ **问题描述**

  1. `异构多任务学习`问题：

     > "This setup of benchmarks allows to formulate a `heterogeneous multitask learning problem` that involves jointly learning all four prediction tasks simultaneously." 这种基准设置允许制定一个异构多任务学习问题，该问题涉及同时共同学习所有四个预测任务。

  2. 具体来说：

     > "These tasks vary in not only output type but also temporal structure: LOS involves a regression at each time step, while in-hospital mortality risk is predicted once early in admission. Their heterogeneous nature requires a modeling solution that can not only handle sequence data but also model correlations between tasks distributed in time. We demonstrate that carefully designed recurrent neural networks are able to exploit these correlations to improve the performance for several tasks." 这些任务不仅在输出类型上有所不同，而且在时间结构上也有所不同：LOS (length of stay) 涉及每个时间步的回归，而院内死亡风险 (in-hospital mortality risk) 是入院早期一次性预测。它们的异构性质需要一种建模解决方案，该解决方案不仅可以处理串行数据，还可以对按时间分布的任务之间的相关性进行建模。我们证明精心设计的循环神经网络能够利用这些相关性来提高多项任务的性能。”
  
+ **数据**

  > "We compile a subset of the MIMIC-III database containing more than 31 million clinical events that correspond to 17 clinical variables listed in the first column of Table 1. These events cover 42276 ICU stays of 33798 unique patients. We define four benchmark tasks on this subset."我们编制了MIMIC-III数据库的一个子集，其中包含超过3100万个临床事件，对应于表1第一列中列出的17个临床变量。这些事件涵盖了 33798 名独特患者的 42276 次 ICU 住院。我们在此子集上定义了四个基准任务。
  >
  > + `17个临床变量`：Capillary refill rate(毛细管再充盈率)，Diastolic blood pressure(舒张压)，Fraction inspired oxygen(馏分吸入氧气)，Glascow coma scale eye opening(格拉斯哥昏迷指数睁眼反应)，Glascow coma scale motor response(格拉斯哥昏迷指数运动反应)，Glascow coma scale total(格拉斯哥昏迷指数)，Glascow coma scale verbal response(格拉斯哥昏迷指数说话反应)，Glucose(葡萄糖)，Heart Rate(心率)，Height(身高)，Mean blood pressure(平均血压)，Oxygen saturation(血氧饱和度)，Respiratory rate(呼吸率)，Systolic blood pressure(收缩压)，Temperature(温度)，Weight(体重)，pH

  | ![image-20230407091827552](https://raw.githubusercontent.com/DaLei001/DaleiPic/main/202304070921606.png) |
  | ------------------------------------------------------------ |
  | 17个选定的临床变量。第二列显示来自 MIMIC-III 数据库的变量的源表。第三列列出了我们在插补步骤中基线中使用的“正常”值，第四列描述了基于 LSTM 的基线如何处理变量。 |

  

+ **基准任务**

  1. `In-hospital mortality prediction` – predicting in-hospital mortality based on the first 48 hours of an ICU stay. This is a binary classification task with area under the receiver operating characteristic (AUC-ROC) being the main metric.`“院内死亡率预测”` – 根据 ICU 住院的前 48 小时预测院内死亡率。这是一个`二元分类`任务，以接收器工作特性 （AUC-ROC） 下的面积是主要指标。
  2. `Decompensation prediction` – predicting whether the patient’s health will rapidly deteriorate in the next 24 hours. The goal of this task is to replace early warning scores currently used in the hospitals. Due to the lack of gold standard for evaluating the early warning scores, we follow earlier work5 and define our task as mortality prediction in the next 24 hours at each hour of an ICU stay. It is important to note that this definition deviates from the core meaning of decompensation, and the task becomes similar to the first one. On the other hand, we believe this is the closest proxy task for decompensation prediction for which one can obtain precise labels from MIMIC-III database. Each instance of this task is a binary classification instance. Likewise in-hospital mortality prediction, the main metric is AUC-ROC. `“失代偿预测”` – 预测患者的健康状况是否会在未来 24 小时内迅速恶化。这项任务的目标是取代目前医院使用的预警评分。由于缺乏评估早期预警评分的黄金标准，我们遵循早期的工作，并将我们的任务定义为在ICU住院期间每小时预测未来24小时内的死亡率。需要注意的是，这个定义偏离了失代偿的核心含义，任务变得与第一个相似。另一方面，我们认为这是最接近失代偿预测的代理任务，可以从MIMIC-III数据库中获得精确的标签。此任务的每个实例都是一个`二元分类`实例。与院内死亡率预测一样，主要指标是AUC-ROC。
  3. `Length-of-stay prediction` – predicting remaining time spent in ICU at each hour of stay. Accurate prediction of the remaining length-of-stay is important for scheduling and hospital resource management. We frame this as `a classification problem` with 10 classes/buckets (one for ICU stays shorter than a day, seven day-long buckets for each day of the first week, one for stays of over one week but less than two, and one for stays of over two weeks). The main metric for this task is Cohen’s linear weighted kappa score. `住院时间预测` – 在住院时间每小时预测在 ICU 的剩余时间。准确预测剩余住院时间对于日程安排和医院资源管理非常重要。我们将其描述为10个类别/桶的`分类问题`（一个用于ICU停留时间少于一天，七个用于第一周的每一天，一个用于住院超过一周但少于两周，一个用于住院超过两周）。这项任务的主要指标是科恩的线性加权kappa分数。
  4. `Phenotype classification` – classifying which of 25 acute care conditions (described in Table 2) are present in a given patient ICU stay record. This problem is a multilabel classification problem with macro-averaged AUC-ROC being the main metric. `表型分类` – 对给定患者 ICU 住院记录中存在 25 种急性护理病症（如表 2 中所述）中的哪一种进行分类。这个问题是一个`多标签分类问题`，以宏观平均AUC-ROC为主要指标。

+ **基准任务的总结**

  > Our benchmark prediction tasks include four in-hospital clinical prediction tasks: `modeling risk` of mortality shortly after admission, `real-time prediction` of physiologic decompensation, `continuous forecasting` of patient LOS, and phenotype `classification`. 我们的基准预测任务包括四个院内临床预测任务：入院后不久的死亡风险建模、生理失代偿的实时预测、患者 LOS 的连续预测和表型分类。

  > These clinical problems also encompass a range of machine learning tasks, including `binary and multilabel classification`, `regression`, and `time series modeling`, and so are of interest to data mining researchers. 这些临床问题还包括一系列机器学习任务，包括二进制和 多标签分类、回归和时间串行建模，因此数据挖掘研究人员对此很感兴趣。

+ **讨论**

  > "In this paper we proposed four `standardized benchmarks` for machine learning researchers interested in clinical data problems, including in-hospital mortality, decompensation, length-of-stay, and phenotype prediction. Our benchmark data set is similar to other MIMIC-III patient cohorts described in machine learning publications but makes use of a larger number of patients and is immediately accessible to other researchers who wish to replicate our experiments or build upon our work. We also described several strong baselines for our benchmarks. We have shown that LSTM-based models significantly outperform linear models, although we expect to see better performing linear models by using more complex feature engineering. We have demonstrated the <u>*advantages of using channel-wise LSTMs and learning to predict multiple tasks using a single neural model*</u>. Our results indicate that <u>*the phenotyping and length-of-stay prediction tasks are more challenging and require larger model architectures than mortality and decompensation prediction tasks.*</u> Even small LSTM models easily overfit the latter two problems." 在本文中，我们为对临床数据问题感兴趣的机器学习研究人员提出了四个标准化基准，包括住院死亡率、失代偿、住院时间和表型预测。我们的基准数据集类似于机器学习出版物中描述的其他 MIMIC-III 患者队列，但使用了更多的患者，并且其他希望复制我们的实验或以我们的工作为基础的研究人员可以立即访问。我们还为我们的基准描述了几个强大的基线。我们已经证明，基于 LSTM 的模型明显优于线性模型，尽管我们希望通过使用更复杂的特征工程来看到性能更好的线性模型。我们已经展示了使用信道 LSTM 和学习使用单个神经模型预测多个任务的优势。我们的结果表明，表型和住院时间预测任务比死亡率和失代偿预测任务更具挑战性，需要更大的模型架构。即使是小型 LSTM 模型也很容易过拟合后两个问题。

</details>

---

### 论文2：{Purushotham, 2018 #5173}

**`"Benchmarking deep learning models on large healthcare datasets"`**

**`"在大型医疗保健数据集上对深度学习模型进行基准测试"`**

- 【2018】【[:memo:](https://www.sciencedirect.com/science/article/pii/S1532046418300716)】【[:gear:]()】【 :mortar_board: `University of Southern California`, `Tsinghua University` 】【:car: `MIMIC-III` 】
- 【_`clinical time series data`, `electronic health records`, `MIMIC-III`, `benchmark`_】

<details>
  <summary>Click to expand</summary>



+ **摘要**
  + 介绍了benchmark 数据集 MIMIC-III和数据集的处理办法，同时介绍了三种benchmark任务以及预测算法，包括传统的得分方法、机器学习模型集成（超级学习者算法）、深度学习方法（反向传播、RNN以及前两者结合的方法），并使用对应的指标评估算法的性能。

</details>

---








## 待读论文

