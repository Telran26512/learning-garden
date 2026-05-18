# RL 基础设施学习计划 —— 从 Java/前端到 RL Infra（52 周）

一份为期一年的路线图：从 Java + 前端技能起步，最终具备构建并参与现代 RL 基础设施的能力（包括 LLM RLHF 系统）。设计投入为 **约 15–20 小时/周**。

---

## 你即将进入什么领域 —— 坦诚预期

你要按顺序学习 **四** 件事：

1. **Python 和科学 Python 技术栈**（NumPy、PyTorch、Jupyter）—— 你的 Java 技能会比你想象中迁移得更多，但 Pythonic 习惯和张量思维确实是全新的。
2. **数学**（线性代数、微积分、概率）—— 不能绕开。这是最大的短板，也是最不能走捷径的部分。
3. **机器学习和深度学习** —— 先监督学习，再深度学习，最后 RL。
4. **分布式系统 + ML 系统工程** —— 只有在你理解集群上到底运行着什么之后再学。

RL 基础设施是 ML 里最*深*的专业方向之一。它位于 RL 理论、分布式系统、GPU 编程，以及如今的 LLM serving 的交叉点。它值得投入 —— 这些是当前 ML 领域杠杆最高的一类工作 —— 但上手坡度是真实存在的。

好消息是：你已有的技能会给你一些大多数 ML 初学者没有的优势：
- 你已经懂编程。循环、类、异步、线程、构建系统、IDE、git、调试 —— 这些都会迁移。
- 你能流畅阅读代码。很多 ML 学习者是在学 ML 的同时学写代码，结果两边都不扎实。
- 前端经验意味着你能构建可视化和仪表盘（这对 RL 训练监控非常有用）。

---

## 阶段概览

| 阶段 | 周数 | 主题 | 目标 |
|-------|-------|-------|------|
| 0 | 1–6   | Python + 数学基础 | 能舒适地使用 Python；能处理所需数学 |
| 1 | 7–12  | 经典 ML | 从零训练分类器；理解梯度下降 |
| 2 | 13–20 | 深度学习 + PyTorch | 训练 CNN 和一个小型 transformer |
| 3 | 21–28 | RL 基础 | 从零实现 DQN、PPO |
| 4 | 29–32 | 现代 RL 算法 | SAC、GAE、探索 |
| 5 | 33–38 | 分布式系统 + GPU | DDP、FSDP、性能分析 |
| 6 | 39–44 | 经典 RL 基础设施 | IMPALA、Ape-X、RLlib |
| 7 | 45–50 | LLM RL / RLHF 基础设施 | GRPO、vLLM、verl |
| 8 | 51–52 | Capstone | 发布一个真实项目 |

---

# 阶段 0 —— Python + 数学基础（第 1–6 周）

目标：能够像熟手一样阅读和编写 Python，并具备足够的数学基础，读 ML 论文时不会慌。

## 第 1 周 —— 面向 Java 开发者的 Python

**概念（Java → Python 对照）**
- 没有强制类型（或使用 `typing` 模块做类型提示）；没有编译步骤。
- 缩进定义代码块（没有花括号）。
- 一切都是对象；用 `dict` 和 `list` 替代 `HashMap` 和 `ArrayList`。
- 列表/字典推导式：`[x*2 for x in xs if x > 0]`。
- 元组、切片、解包。
- 函数是一等对象；闭包；装饰器（可以类比 Java 注解，但更强大）。
- 迭代器、生成器（`yield`）。
- `with` 语句（资源管理）。
- GIL：Python 线程不会在 CPU 任务上真正并行；应使用 `multiprocessing`。

**资源**
- Luciano Ramalho 的 *Fluent Python* —— 第 1–8 章。对有经验程序员来说，这是最好的 Python 书。
- 在线搜索 “Python for Java Developers” 速查表，用来做语法映射。

**代码**
- 用 Python 重新实现你自己的一个小型 Java 项目。留意哪些地方更短，哪些地方更奇怪。
- 环境配置：`uv`（现代包管理器，比 pip 更快）、带 Python 扩展的 VS Code、Jupyter。

**交付物：** 一个你自己写的小型 CLI 工具或脚本，风格符合 Python 习惯。约 200–500 行。

## 第 2 周 —— NumPy 和科学计算技术栈

这一周比看上去更重要。**张量思维就是 ML 思维。**

**概念**
- NumPy 数组 vs Python 列表 vs Java 数组：连续内存、向量化操作、广播。
- shape、dtype、axis。reshape、transpose、切片。
- 广播规则（初学者 bug 的第一大来源）。
- 向量化操作：`a + b`、`a @ b`、`np.sum`、`np.mean` —— 这些会替代循环。
- 用 `matplotlib` 画图。
- 用 `pandas` 处理表格数据（先简单了解，之后会回来用）。
- Jupyter notebooks：cell、kernel、`%timeit`、`%matplotlib inline`。

**资源**
- Jake VanderPlas 的 *Python Data Science Handbook* —— 第 2–4 章。在线免费。
- NumPy 官方的 “NumPy for absolute beginners” 指南。

**代码练习**
- 用三种方式重新实现矩阵乘法：三重循环、NumPy，然后计时。观察 100×–1000× 的速度提升。
- 用 NumPy 生成 Mandelbrot 集（不要对像素写循环）。
- 用 pandas 加载 CSV，计算 group-by 统计量，并用 matplotlib 画图。

**交付物：** 一个 Jupyter notebook：加载数据集、计算统计量，并生成 3 张图。

## 第 3 周 —— 线性代数（实用部分）

你不需要数学学位。你需要**看见**这些操作到底在*做什么*。

**概念**
- 向量和向量空间；点积、范数。
- 矩阵作为线性变换。
- 矩阵乘法：行 × 列的直觉，batch 视角。
- 特征值和特征向量（直觉：不会被旋转的方向）。
- 秩、零空间、张成空间（轻量覆盖）。
- SVD（奇异值分解）—— 在 ML 中反复出现。

**资源**
- 3Blue1Brown，*Essence of Linear Algebra* —— 全系列。**必看。** 每个视频都看。
- Gilbert Strang 的 MIT 18.06 —— 想加深理解可选。

**代码**
- 使用 NumPy 和 SVD 从零实现 PCA。应用到 MNIST（投影到 2D 并可视化）。
- 用两种方式实现线性回归：正规方程和梯度下降。

**交付物：** PCA + 回归 notebook，并用你自己的话写解释。

## 第 4 周 —— 微积分和优化

**概念**
- 导数和偏导数。
- 梯度是最陡上升方向。
- 链式法则（反向传播的核心）。
- 凸性；为什么凸问题容易，而神经网络不是。
- 梯度下降及其变体：SGD、momentum、Adam。
- 为什么使用随机 mini-batch。

**资源**
- 3Blue1Brown，*Essence of Calculus* —— 第 1–6 集。
- Sebastian Ruder 的博客文章：“An overview of gradient descent optimization algorithms.”

**代码**
- 在一个 2D 函数上从零实现梯度下降（例如 Rosenbrock）。画出轨迹。
- 用 SGD 实现线性回归（不用 NumPy 的 `lstsq`）。
- 按论文实现 Adam（Kingma & Ba 2014）。约 30 行。

**交付物：** 一个 “gradient descent zoo” notebook，对比 SGD、momentum、Adam 在玩具函数上的表现，并配图。

## 第 5 周 —— 概率和统计

**概念**
- 随机变量、分布（Bernoulli、Gaussian、categorical）。
- 期望和方差。
- 条件概率、贝叶斯公式。
- 最大似然估计。
- KL 散度和交叉熵（你会在**每个** ML loss function 里见到它们）。
- 重参数化技巧（现在模糊也没关系，之后还会再次遇到）。

**资源**
- Deisenroth、Faisal、Ong 的 *Mathematics for Machine Learning* —— 第 6–8 章。在线免费。
- StatQuest YouTube 频道 —— 最适合理解直觉。

**代码**
- 用 `numpy.random` 从各种分布采样。画直方图。
- 从零实现 logistic regression（这是你的第一个 ML 模型）。在 2D 玩具数据集和 breast cancer 数据集（sklearn）上训练。
- 手动验证：推导 logistic regression 的 cross-entropy loss 梯度。

**交付物：** 一个 logistic regression notebook，在 markdown cell 中写完整数学推导。

## 第 6 周 —— 缓冲 / 补课 / 小项目

现实情况是：到这里你很可能已经落后了。用这一周来：
- 复习第 1–5 周的薄弱点。
- 做一个小型端到端项目：加载数据集、做 EDA、训练 logistic regression、评估、写总结。
- 做一个 Kaggle “Getting Started” 比赛（Titanic）。

**检查点：** 你能读懂下面这段话并感到自在吗？

> “我们在训练集上用 mini-batch SGD 最小化 cross-entropy loss，其中每个样本是一个向量 x ∈ ℝ^d，标签 y ∈ {0,1}，参数 θ ∈ ℝ^d，预测值 ŷ = σ(θ · x).”

如果可以，继续前进。如果不行，再多花一周。

---

# 阶段 1 —— 经典机器学习（第 7–12 周）

目标：在接触深度学习之前，端到端理解监督学习。这个阶段很短，但很关键。

## 第 7 周 —— 监督学习基础

**概念**
- train/validation/test split。为什么三者都存在。
- bias-variance tradeoff。过拟合和欠拟合。
- 正则化：L1、L2。
- 交叉验证。
- 评估指标：accuracy、precision/recall、F1、ROC/AUC、MSE、MAE、R²。

**资源**
- Coursera 上 Andrew Ng 的 *Machine Learning*（旧版，但仍然经典）。
- *An Introduction to Statistical Learning*（ISLR）—— 第 2–6 章。在线免费。

**代码**
- 在表格数据集上训练 linear regression、ridge、lasso。进行比较。
- 画 learning curves（train vs val loss），诊断过拟合/欠拟合。

## 第 8 周 —— 经典算法

**概念**
- 决策树（直觉：按坐标轴方向切分）。
- 随机森林、梯度提升（XGBoost、LightGBM）。
- k-NN、朴素贝叶斯、SVM。
- k-means、层次聚类。

**资源**
- ISLR —— 第 7–9、12 章。
- scikit-learn 用户指南。

**代码**
- 在一个真实 Kaggle 数据集上训练 XGBoost 模型。超过你的 logistic regression baseline。
- 从零实现决策树（1D 版本就够）。理解 information gain。

## 第 9 周 —— 更深入的优化和损失函数

**概念**
- 损失函数：MSE、MAE、Huber、cross-entropy、hinge、focal。
- 为什么不用 accuracy 当 loss（不可微）。
- 优化器细节：learning rate schedule、weight decay、gradient clipping。
- 初始化：为什么重要；Xavier、He init。

**代码**
- 用 NumPy 从零实现一个 MLP（手写 forward + backward）。一层 hidden layer 即可。
- 在 MNIST 上训练。达到 >95% accuracy。

**交付物：** 手写 NumPy MLP + backprop。对大多数人来说，这是 ML “点击到位”的时刻。

## 第 10 周 —— PyTorch 入门

你已经配得上 PyTorch 了。

**概念**
- Tensor = NumPy + autograd + GPU。
- `torch.nn.Module`、参数、`forward()`。
- `Dataset`、`DataLoader`。
- optimizer + loss + training loop 的基本模式。
- 保存/加载模型。

**资源**
- PyTorch 官方 “Learn the Basics” 教程。
- Stevens、Antiga、Viehmann 的 *Deep Learning with PyTorch*。

**代码**
- 用 PyTorch 重新实现第 9 周的 MLP。应该约 50 行。
- 添加 validation loop、early stopping、learning rate scheduler。
- 先训练 MNIST，再训练 Fashion-MNIST，再训练 CIFAR-10。

## 第 11 周 —— 卷积神经网络

**概念**
- 卷积操作；感受野；padding/stride。
- pooling（max、average）。
- 经典架构：LeNet、AlexNet、VGG、ResNet。
- skip connection（ResNet 背后的思想）。
- data augmentation、batch normalization、dropout。

**资源**
- Stanford CS231n —— 第 5–9 讲（有视频）。
- Karpathy 的 “A Recipe for Training Neural Networks” 博客文章 —— 必读，长期适用。

**代码**
- 在 CIFAR-10 上训练一个小型 CNN。test accuracy 达到 >85%。使用 augmentation、batchnorm、LR scheduling。
- 在你选择的小数据集上 fine-tune 一个 pretrained ResNet18。

**交付物：** 完整 CIFAR-10 训练 pipeline，带规范日志（TensorBoard 或 Weights & Biases）。

## 第 12 周 —— 阶段 1 Capstone

任选一个：
- 在 Kaggle 比赛上训练图像分类器；提交结果。
- 在真实数据集上构建表格 ML pipeline（feature engineering + XGBoost）。
- 构建一个小型前端（React），调用 Python ML 模型 API。这里用上你的既有技能。

**检查点：** 你应该能够：(a) 凭记忆写 PyTorch training loop，(b) 端到端解释 backprop，(c) 读懂并跟上 ML 博客文章。

---

# 阶段 2 —— 深度学习深入（第 13–20 周）

目标：在进入 RL 之前建立深度学习直觉。RL 论文默认你已经训练过 transformer。

## 第 13 周 —— 训练动态和调试

**概念**
- 神经网络为什么能训练起来（简要：NTK、lottery tickets 等 —— 只要有直觉）。
- 常见失败：vanishing/exploding gradients、dead ReLUs、NaN losses。
- 调试 checklist（再次使用 Karpathy 的 recipe）。
- mixed precision training（fp16/bf16）。

**代码**
- 故意把训练弄坏（坏初始化、坏 LR、无 normalization）。观察每种失败模式。
- 用 `torch.profiler` 分析一次训练运行。

## 第 14 周 —— 序列模型和 RNN

**概念**
- 序列建模问题：语言、时间序列、音频。
- RNN、LSTM、GRU。为什么它们难训练。
- teacher forcing。
- embeddings。

**代码**
- 在 Shakespeare 上训练 character-level LSTM。生成文本。Karpathy 的经典练习。

这在今天主要是历史内容，但你需要它来理解为什么 transformer 值得珍惜。

## 第 15 周 —— Transformer（Attention Is All You Need）

**概念**
- self-attention：query、key、value。
- multi-head attention；positional encodings。
- encoder-decoder vs decoder-only。
- residual stream、LayerNorm。
- 为什么 transformer 取代了 RNN。

**资源**
- Vaswani et al. 2017，*Attention Is All You Need*。
- Karpathy 的 *Let's build GPT from scratch* 视频 —— 必看。
- Jay Alammar 的 *The Illustrated Transformer*。
- Sasha Rush 的 *The Annotated Transformer*。

**代码**
- 跟着 Karpathy 的 nanoGPT 视频，自己从零构建。
- 在 TinyShakespeare 上训练一个 tiny transformer（约 10M 参数）。生成 samples。

## 第 16 周 —— 现代 LLM 高层概览

**概念**
- pretraining → SFT → RLHF（预览）。
- tokenization（BPE、SentencePiece）。
- KV cache，以及为什么推理很特殊。
- scaling laws（Chinchilla）。
- LoRA 和 PEFT。

**资源**
- HuggingFace “NLP Course” —— 第 1–3 章。
- Sebastian Raschka 关于 LLM internals 的博客文章。

**代码**
- 用 HuggingFace `transformers` + `peft` fine-tune 一个小型 pretrained model（例如 GPT-2 small，或 Qwen2.5-0.5B）。
- 运行 inference；测量 tokens/sec。

## 第 17 周 —— 面向硬件的 ML

这是你通往 systems work 的桥。

**概念**
- GPU vs CPU：SIMT、内存层级。
- HBM、L2、register；arithmetic intensity。
- bottleneck：compute-bound vs memory-bound。
- 为什么 batching 很重要（matmul throughput）。
- fused kernels、FlashAttention。

**资源**
- Horace He，*Making Deep Learning Go Brrrr From First Principles* —— 必读。
- FlashAttention 论文（Dao et al. 2022）—— 为直觉而读，不是为了实现。

**代码**
- profile 你的 tiny transformer 训练。找到 bottleneck。尝试 `torch.compile`。再次 profile。

## 第 18 周 —— 可复现性、日志、轻量 MLOps

**概念**
- seeding（`torch.manual_seed`、deterministic ops）。
- experiment tracking：Weights & Biases、MLflow、TensorBoard。
- config management：Hydra、OmegaConf。
- model versioning。

**代码**
- 用规范 seed 和 W&B logging 重新运行之前的一个实验。验证它可复现。

## 第 19–20 周 —— 阶段 2 Capstone

任选一个：
- 复现 nanoGPT 的 character-level 结果，然后扩展（不同数据集，或不同架构改动）。
- 用 LoRA 在一个小型自定义数据集上 fine-tune 一个 1B 参数 LLM。构建 Gradio 前端（你的前端技能会发光）。
- 在真实数据集上用 transformer backbone（ViT）训练图像分类器。超过阶段 1 的 CNN baseline。

**检查点：** 你可以阅读近期 ML 论文（例如一篇 2024–2025 的 LLM 论文）并跟上约 70%。剩下 30% 是 RL。

---

# 阶段 3 —— RL 基础（第 21–28 周）

你终于到了。从这里开始，这份计划会在基础已经到位的情况下，对齐原始路线。

## 第 21 周 —— MDP 和动态规划

**概念**
- 马尔可夫决策过程：states、actions、transitions、rewards、discount。
- value functions、Bellman equations。
- policy iteration、value iteration。

**阅读**
- Sutton & Barto，*Reinforcement Learning: An Introduction* —— 第 1–4 章。
- David Silver 的 RL 课程 —— 第 1–3 讲。

**代码**
- 在 FrozenLake 上实现 value iteration 和 policy iteration。

## 第 22 周 —— Monte Carlo 和 TD Learning

- Sutton & Barto，第 5–7 章。
- 在 Cliff Walking 上实现 tabular Q-learning 和 SARSA。

## 第 23 周 —— DQN

**论文**
- Mnih et al. 2015（DQN）。
- van Hasselt et al. 2016（Double DQN）。
- Schaul et al. 2016（PER）。

**代码**
- 在 CartPole 上实现 vanilla DQN，然后迁移到 LunarLander。
- 参考：CleanRL `dqn.py`。

## 第 24 周 —— Rainbow 变体

- 逐个加入 Double DQN、Dueling、PER。重新 benchmark。

## 第 25 周 —— Policy Gradients

- 在 CartPole 上实现 REINFORCE。
- 用 GAE 实现 A2C。

**论文**
- Williams 1992；Mnih et al. 2016（A3C）；Schulman et al. 2016（GAE）。

## 第 26–27 周 —— PPO 深入

这是整个计划中最重要的算法。

**必读**
- Schulman et al. 2017（PPO）。
- Huang et al.，*The 37 Implementation Details of PPO* —— 必读。
- Engstrom et al. 2020，*Implementation Matters in Deep RL*。

**代码**
- 从零实现 PPO，先 CartPole，再 LunarLander，再 HalfCheetah。
- 逐行注释 CleanRL 的 `ppo_continuous_action.py`。

**交付物：** 一个 README，把 37 个 PPO 细节与你的实现逐项对勾。

## 第 28 周 —— 阶段 3 回顾 + 小项目

- 在一个你没尝试过的 Gymnasium 任务上训练 PPO。
- 用你自己的话写一篇博客风格的 PPO 解释文章。

---

# 阶段 4 —— 现代 RL 算法（第 29–32 周）

## 第 29 周 —— Off-Policy Continuous Control（SAC、TD3）

**论文**
- Lillicrap et al. 2016（DDPG）。
- Fujimoto et al. 2018（TD3）。
- Haarnoja et al. 2018（SAC）。

**代码**
- 在 HalfCheetah 上实现 SAC。1M steps 内达到约 9000 reward。

## 第 30 周 —— Distributional、Offline、Model-Based（广度周）

- 快速浏览 C51、Dreamer v3、MuZero、IQL、CQL。
- 在 D4RL 数据集上运行 IQL。

## 第 31 周 —— Multi-Agent 和 Exploration

- MADDPG、QMIX、MAPPO（快速浏览）。
- RND、ICM、NGU。

## 第 32 周 —— 阶段 4 Capstone

- 在一个非平凡 MuJoCo 任务上训练 SAC。
- 将你的结果与 CleanRL benchmark 对比。

---

# 阶段 5 —— 分布式系统与 ML 系统（第 33–38 周）

## 第 33 周 —— 分布式计算基础

**概念**
- process vs thread；Python 的 GIL；multiprocessing。
- IPC：pipes、shared memory、message queues。
- networking：TCP、gRPC、ZeroMQ。

**阅读**
- Kleppmann 的 *Designing Data-Intensive Applications* —— 第 1、4、8 章。

**代码**
- 用原始 socket 构建一个玩具 parameter-server。

## 第 34 周 —— Ray Framework

- Ray actors、remote functions、object store。
- 阅读 Ray 论文（Moritz et al. 2018）。
- 用 Ray 构建一个小型分布式任务。

## 第 35 周 —— GPU 和 PyTorch 内部机制

- CUDA 基础、streams、kernels。
- PyTorch dispatcher、autograd。
- mixed precision、activation checkpointing。

## 第 36 周 —— 分布式训练（DDP、FSDP）

**阅读**
- PyTorch DDP 论文（Li et al. 2020）。
- ZeRO 论文（Rajbhandari et al. 2020）。
- FSDP 论文（Zhao et al. 2023）。

**代码**
- 用 DDP 跨 2 张 GPU 训练 multi-GPU PPO。

## 第 37 周 —— Tensor 和 Pipeline Parallelism

- Megatron-LM 论文（Shoeybi et al. 2019）。
- GPipe（Huang et al. 2019）。
- 用 FSDP 训练一个小型模型。

## 第 38 周 —— Vectorized Environments 和吞吐量

- EnvPool、Brax、Isaac Gym。
- 在多种配置下 benchmark CartPole throughput。
- 在 GPU 上端到端运行 Brax `humanoid` 的 PPO。

---

# 阶段 6 —— 经典 RL 基础设施（第 39–44 周）

## 第 39 周 —— Actor-Learner 架构

**论文**
- IMPALA（Espeholt et al. 2018）。
- SEED RL（Espeholt et al. 2020）。

**代码**
- 使用 `torch.multiprocessing` 在 CartPole 上实现 IMPALA。

## 第 40 周 —— V-trace 和 Off-Policy Correction

- 加入 V-trace correction；验证稳定性。

## 第 41 周 —— Distributed Replay（Ape-X、R2D2）

**论文**
- Ape-X（Horgan et al. 2018）。
- R2D2（Kapturowski et al. 2019）。

**代码**
- 构建一个 Ape-X 风格系统：8 个 actors + GPU learner。

## 第 42 周 —— Ray RLlib

- 阅读 RLlib 论文（Liang et al. 2018）。
- 用 RLlib 复现 Atari Pong 上的 IMPALA。
- 从 `train()` 到 gradient update，trace 一个 training step。

## 第 43 周 —— 其他 Frameworks

- TorchRL、CleanRL、SB3、Sample Factory。
- 用四个框架分别训练同一个任务；进行比较。

## 第 44 周 —— Performance Engineering

- 端到端 profile 你的 IMPALA 实现。
- 目标：不改算法，实现 2× speedup。
- 写下 before/after。

---

# 阶段 7 —— LLM RL / RLHF 基础设施（第 45–50 周）

## 第 45 周 —— RLHF 基础

**论文**
- Christiano et al. 2017（RL from preferences）。
- Ouyang et al. 2022（InstructGPT）—— 必读。
- Bai et al. 2022（Constitutional AI / RLAIF）。

**代码**
- 阅读 TRL 的 `PPOTrainer` 源码。
- 在 1B 模型上运行一个 tiny RLHF：SFT → RM → PPO。

## 第 46 周 —— DPO、GRPO 和现代 Alignment

**论文**
- Rafailov et al. 2023（DPO）。
- Ethayarajh et al. 2024（KTO）。
- Shao et al. 2024（GRPO / DeepSeekMath）。
- DeepSeek-R1（2025）。

**代码**
- 用 <200 行从零实现 DPO。
- 在 GSM8K 上用小模型从零实现 GRPO。

## 第 47 周 —— Inference + Training Engine Coupling

**概念**
- 用 vLLM / SGLang 做 rollouts；用 FSDP / Megatron 做 training。
- weight sync（NCCL broadcast、CUDA-IPC）。
- colocated vs disaggregated。
- sync vs async RL；staleness；off-policy correction。

**阅读**
- vLLM 论文（Kwon et al. 2023）。
- SGLang 论文（Zheng et al. 2024）。
- AReaL（2024–2025）和其他 async RL 论文。

## 第 48 周 —— Frameworks 深入

- verl（Bytedance, HybridFlow）。
- OpenRLHF（基于 Ray）。
- NeMo-Aligner（NVIDIA）。
- TRL（HuggingFace）。

**代码**
- 阅读 verl 源码；trace 一个完整 training step。
- 用 OpenRLHF 在 3B 模型上运行 GRPO。

## 第 49 周 —— 比较 Frameworks

- 在两个框架中实现同一个任务（GSM8K 上的 GRPO）。
- 比较 throughput、memory、code size、ergonomics。

## 第 50 周 —— Production Concerns

- reward hacking、length bias、mode collapse。
- multi-turn RL；agentic rollouts；tool use。
- verifiable vs preference rewards。
- PRMs vs ORMs。
- long-context RL infra（CP + SP）。
- 在数百张 GPU 规模下的 fault tolerance、checkpointing、elastic scheduling。

**阅读：** 近期 technical reports（Llama-3、Qwen2.5/3、Tülu 3、Magistral、DeepSeek-R1）。

---

# 阶段 8 —— Capstone（第 51–52 周）

## 第 51 周 —— Build

任选**一个**：

**Option A —— 经典路线：** 构建一个极简 IMPALA + V-trace + distributed replay framework，使用 PyTorch，少于 2000 行。通过 `torch.distributed` 支持 multi-node。复现一个体面的 Atari 分数。

**Option B —— LLM RL 路线：** 构建一个极简 GRPO trainer，使用 vLLM 做 rollouts、FSDP 做 training，包含 NCCL weight sync、async rollouts，在 GSM8K 上训练 1.5B 模型达到 >70% accuracy。

**Option C —— 前端优势路线：** 在真实 RL framework 之上构建一个实时 RL training dashboard（React + WebSocket）。可视化 trajectories、value functions、gradient norms、throughput。这是利用你前端技能的独特角度，而 RL 社区确实缺少好的可视化工具。

## 第 52 周 —— Polish、Write-up、Open Source

- 清理 repo：README、reproducibility、benchmarks、plots。
- 写一篇 2000–4000 字博客，介绍你构建的东西。
- 至少向一个真实 RL framework 提交一个 PR（TRL、OpenRLHF、verl、RLlib、TorchRL、CleanRL）。
- 明确下一阶段你的 specialization。

---

## 到最后你将已经实现或读过的算法

**经典 RL：** Q-learning、SARSA、DQN、Double DQN、Dueling、PER、REINFORCE、A2C、PPO、TRPO（读过）、DDPG（读过）、TD3（读过）、SAC。

**分布式 RL：** IMPALA + V-trace、Ape-X、R2D2（读过）、SEED RL（读过）、Sample Factory（读过）。

**LLM RL：** PPO-for-LLMs、DPO、KTO、RLOO、GRPO、DAPO（读过）。

**相邻主题（读过但未实现）：** C51、Dreamer、MuZero、IQL、CQL、MAPPO、RND。

---

## 如何保持进度

**每周节奏**
- 周一至周五：每天 2–3 小时阅读和编码。
- 周六：4–6 小时较长的构建时段。
- 周日：复盘本周学习、写笔记、规划下周。

**每月检查点**
- 写一页反思：哪些东西想通了，哪些没有，哪些需要回头补。
- 更新 GitHub：每周至少应该在某处增加一个 commit。

**要避免的反模式**
- *Tutorial hell.* 第 6 周之后，你应该在写代码，而不是只阅读。
- *跳过数学。* 到阶段 3 你会后悔。现在补。
- *跳过经典 RL。* 很多人想直接跳到 RLHF。他们很快就会撞到上限。
- *不完成实现。* “我读过论文” ≠ “我实现过它。” 去实现它。
- *过度工具化。* 第 4 周你不需要 Kubernetes。你永远也不需要 homelab GPU cluster —— 到阶段 5 之前，Colab + 单张租来的 GPU 足够。

**硬件**
- 阶段 0–3：笔记本就够。GPU 用 Colab 或 Kaggle。
- 阶段 4 开始：租一张云 GPU（RunPod、Lambda、Vast.ai）即可。预算约 $30–100/月。
- 阶段 7：偶尔会想用单张 A100 或 H100。spot/preemptible 价格便宜。

**社区**
- EleutherAI Discord —— 对新手最友好的 ML 研究社区。
- CleanRL Discord —— 专门面向 RL。
- HuggingFace forums —— 面向 LLM/RLHF。
- 阅读 verl、OpenRLHF、vLLM repo 上的每个 issue 和 PR。这比大多数教程更有价值。

---

## 你的 Java/前端背景可以让你跳过或压缩的内容

- **配置开发环境、IDE、git workflows：** 几分钟，不是几天。
- **理解 async、threads、processes、build systems：** 大部分能映射过来。
- **阅读大型代码库：** 这是多数 ML 初学者缺少的能力。你不缺。
- **构建可视化：** 当课程要求 “make a plot” 时，你很自然会做出一个*好的*图。Capstone Option C 正是因为这个优势而存在。

## 你不能跳过的内容

- **数学。** 线性代数、微积分、概率。把时间花进去。
- **从零实现算法。** 读论文会让人感觉很有效率，但不一定转化成能力。
- **profile 和 debug 真实训练运行。** 这才是 RL infra engineer 的实际工作。
- **阅读 framework 源码。** verl、OpenRLHF、RLlib、TRL —— 你在这里的熟练度，才是让你在这个领域可雇佣的关键。

---

## 最后的想法

一年很长。大多数人会在第 12 周（数学刚变难之后）或第 30 周（RL 训练令人沮丧时）放弃。如果你越过这两堵墙，你就会进入一小群真正能做这项工作的人当中。

这个技能组合对应的就业市场，是当前软件领域里最强的之一。值得。
