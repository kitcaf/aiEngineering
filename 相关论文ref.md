# 核心

路由，
（1）怎么路由？ agentNet
（2）怎么支持路由？


# Decentralization MAS

# 一、以去中心化为核心的协作方法

## AgentNet：自主执行与路由-经验驱动型 nips 2025

代码仓库：[zoe-yyx/AgentNet: [NIPS2025] A decentralized, RAG-enhanced multi-agent framework for LLMs with dynamic task routing and agent evolution.](https://github.com/zoe-yyx/agentnet)

> 去掉中心控制器，让 Agent 自己形成网络，通过经验学习和动态路由完成任务

### 研究问题
如何设计一种无需中心控制器、能够自主进化、自适应协作，并且支持大规模扩展的 LLM 多智能体系统？

### 主要挑战（/现有系统问题）
> 论文没有直接写，而是一直在强调现有中心化架构下的MAS的不足之处

集中式架构包含以下问题：

- 集中式架构限制扩展性
- 智能体无法动态适应任务变化
- 跨组织知识共享存在隐私障碍
- 智能体无法持续学习和专业化

### 创新点

- 完全去中心化的 Multi-Agent 架构
- 动态演化的 Agent 网络结构
- 基于 RAG 的智能体长期能力进化

### 方法

（一）Decentralized Network Topology + 动态演化的 Agent 网络结构

每个Agent内部增加一个Router模块（路由能力），多个自治智能体通过局部决策形成全局协作


一开始构建全连接，在一次次任务完成的过程中，去更加连接边的强度

- 任务分配和执行
- 更新网络
- 更新Agent能力和记忆

节点能力变化、边权变化、网络结构变化（设置了一个阈值低质量的边删除）

（二）Adaptive Learning and Specialization

每个Agent保存：`历史任务片段`，新任务来了检索Agent自己的历史经验，去更好的执行


### 思考

论文一直在强调/解决的是“如何协作”。没有完全解决“为什么相信这个 Agent？”。即假设智能足够强，去中心化以后就一定可以自然形成高效协作？难道节点自由连接，就会产生秩序嘛？去中心化系统最大的挑战不是通信应该是在Trust、Incentive、Governance

即：去中心化 ≠ 可信 （现在本质似乎是一个自证循环）

可能的迁移方向：

（一）Agent身份与信誉系统，Decentralized Agent Identity (DAI)，不去强调我说我厉害，而是“网络验证我过去贡献”

（二）把 Agent 经验RAG改造成“可信经验链” (Experience Blockchain)

现在AgentNet，`Agent memory`（`task trajectory`）`RAG` 后决定new task , 即找过去类似经验，去找经过共识验证的经验

（三）任务分配 = 区块链市场机制（Agent Task Market）

AgentNet现在任务路由`choose highest capability agent`，只考虑能力，没有考虑成本、可靠性、当前负载等等问题，类似构建一个Agent Task Market，任务发布-Agent竞争后通过智能合约自动选择

（四）Agent之间的共识机制

（五）Agent自治组织 DAO

| 能力 | AgentNet | Blockchain AgentNet |
| --- | --- | --- |
| 去中心化 | ✅ | ✅ |
| 动态协作 | ✅ | ✅ |
| 经验学习 | ✅ | ✅ |
| 经验可信 | ❌ | ✅ |
| Agent身份 | 弱 | 强 |
| 防作弊 | ❌ | ✅ |
| 激励机制 | ❌ | ✅ |
| 跨组织协作 | 部分 | 强 |
| 自治经济 | ❌ | ✅ |


## CoMAS 去中心化共同演化：强化学习型 ICLR 2026

代码仓库：[xxyQwQ/CoMAS: Implementation for the paper "CoMAS: Co-Evolving Multi-Agent Systems via Interaction Rewards".](https://github.com/xxyQwQ/CoMAS)

> CoMAS 是在 去中心化的 MAS（Multi-Agent System）里面引入 RL，**让 agent 通过交互产生训练信号**，然后更新 agent 参数。和传统的RL for MAS的卖点就是 reward （说成是intrinsic reward）

注：论文的核心点不是**去中心化**作为卖点（只在介绍中提了，但是实际上就是预定义流程），而是Rl应用在MAS中


### 研究问题

LLM agent 是否可以仅仅通过多智能体之间的交互，在没有外部 reward 的情况下实现自我进化？

### 不足之处

> 论文不写挑战

始终在强调都是现在RL for MAS 的奖励都是外部奖励，不符合人类自进化规范。因此核心卖点就是怎么样在由MAS内容的agent去进行产生奖励

### 方法

交互阶段 -> 奖励生成阶段 -> 策略优化阶段

（一）交互阶段：hierarchical and decentralized interactions


对于一个问题：进行多轮讨论，每一轮 按照Solution → Evaluation → Scoring3个步骤进行产生一个交互轨迹（每一个步骤抽取agent池里面的agent进行）。把每一轮的轨迹保存 - **预定义流程**

（二）奖励生成阶段

LLM-as-a-judge

score = 3
solution 正确。
evaluation 无效或者错误。

score = 2
solution 基本正确。
但是 evaluation 找到了小问题。

score = 1
solution 错误。
evaluation 找到了关键错误。

（三）Policy Optimization：REINFORCE++

## CoLLM 去中心化执行Actor-Critic 训练型 ICML 2026

代码仓库：[OpenMLRL/CoMLRL: Open-Source Library for Fully Cooperative Multi-LLM Reinforcement Learning](https://github.com/OpenMLRL/CoMLRL)

> 如何有效优化这些去中心化 LLM 智能体，使其能够进行协作，仍然是一个尚未解决的问题

### 研究问题
如何有效训练去中心化（decentralized）的 LLM 多智能体系统，使多个 LLM agent 能够自主协作完成任务？


### 现有不足 / 挑战（Challenges）

- 现有 LLM 多智能体协作过度依赖集中式执行，并且依赖预先设计好的协作流程
- 现有 **MARL 优化方法**不适合长任务协作（**长程奖励**），主要关注点是Reward太晚出现，导致方差很大，agent 不知道哪一步贡献了成功
### 方法

> 核心方法就是使用使用Multi-Agent Actor-Critic 这种方式（Actor-Critic 传统方法） + TD（Temporal Difference） - 解决一次太久了的问题（**即时奖励**），让 Critic 学习价值函数，用价值估计辅助 Actor 更新

将已有的 Multi-Agent Actor-Critic（MAAC）方法应用到去中心化 LLM 协作的微调中，并分析不同 Critic 设计的效果

构建了两种结构：CoLLM-CC 和 **CoLLM-DC**

主要就是训练 Critic

# 统一编排或统一结构设计的协作方法

## 统一路由决策 AgentRouter acl 2026

对于一个固定的下游任务，实践者很难确定应该选择哪个模型或者智能体配置才能获得最佳效果

### 解决的问题
> 如何针对不同 QA 输入，自适应地选择和组合多个 LLM agent，使多智能体系统获得最佳回答性能, given a fixed down stream task, practitioners face substantial uncer tainty about which model or agent configuration is most effective

### 现有不足 / 挑战（Challenges）
- 现有方法忽略了 QA 任务中的丰富结构化上下文信息
- 现有研究主要关注成本效率或者跨任务适应能力，忽略了一些重要场景，比如在有些场景中其实选择协作方案更好，而不是单一智能体
### 创新点
- 将多智能体 QA 转化为知识图谱路由问题
- 提出 Graph-Supervised Collaboration Learning
### 方法

> 本质要做的是给定一个任务，输出最合适解决这个QA任务的agent，就是一个适合解决这个问题的agent选择概率分布，并不是学习学习协作关系
>



## 统一工作流搜索 Aflow ICLR 2025
> https://github.com/FoundationAgents/AFlow
> 如何更好的自动生成智能体工作流
核心方法：将工作流建模为一系列相互连接的 LLM 调用节点，其中每个节点代表一个 LLM 操作，而边定义这些操作之间的逻辑关系、依赖关系以及执行流程
### 研究问题
如何实现智能体工作流（agentic workflow）的自动化生成与优化，使 LLM 能够在减少人工设计成本的情况下，自动发现高性能、适应不同任务的工作流结构。
### 现有方法的不足 / 挑战是什么？
- 不足：现有方法依赖人工设计，无法完全自动化
- 挑战：（1）任务的多变性（2）搜索空间较大
### 创新点
- 将 workflow optimization 转化为搜索问题
- AFLOW 使用 code 作为 workflow edge 表示
- 引入预定义 workflow 操作模块：Generate ...
### 方法
让 LLM 自己成为“工作流设计工程师”，通过搜索和试错，自动设计出解决任务的最佳 LLM 调用流程（LLM 生成/修改“调用 LLM 的（workflow）代码”）
> LLM → 自动生成 workflow → 执行 workflow → 根据结果修改 workflow → 继续搜索 → 找到最优 workflow
Node：一次 LLM 调用
Edge：Edge = 决定 Node 如何连接、如何执行的程序逻辑（code），由代码表示的 workflow 连接逻辑，整个 workflow 的代码表示中，控制 Node 如何调用、如何连接、如何执行的部分，就是 Edge，本质就是大模型去改代码。默认好像就是一个最简单的 workflow template（模板）
比如Review & Revise，一个“改进答案”的操作Generate Node -> Review Node -> Revise Node
Operator：一个已经封装好的“常见 LLM 工作流程模块”（预定义的小型 workflow 模块）

## 通信结构优化 AgentPrune ICLR 2026
> 当前多智能体流程通信存在冗余的情况，造成经济损失
### 研究问题
> 如何降低 LLM 多智能体系统中的通信成本，同时保持甚至提升任务性能
### 问题的挑战 / 现有不足
是一个新提出来的问题，甚至提前做了一个金钱实验，不同通信方法相比简单 chain 结构，会导致 2～11.8 倍 token 消耗增加
链式通信：Agent1 → Agent2 → Agent3 → Agent4
树状通信：让多个 agent 并行探索不同方向
Complete Graph 通信：人类会议，博弈类型的感觉
Optimizable Graph（可优化图）： 让模型自己学习
然后分了一个当前mas通讯分类，我表示质疑
（1）对话内通信
（2）跨对话通信：不同轮次（round）之间的信息传递
```
Round 1
↓
Round 2
↓
Round 3
```
- 不知道哪些通信是有用的
- 通信结构复杂
### 创新点
- 首次提出并定义 Communication Redundancy 冗余
- 提出空间-时间通信图建模
- 提出 AgentPrune 剪枝框架
### 方法
从通信包含空间和时间两个维度构建图，对话内通信空间边、跨对话通信时间边
- 构建 Spatial-Temporal Graph
Spatial Communication和Temporal Communication
- Communication Mask Learning - 每条边增加一个 mask，其实就是边的通讯重要程度表示 矩阵（matrix）实际是有两类矩阵mask 矩阵
- 怎么训练mask ?
    loss（应该是reward）: 任务正确loss，通信稀疏 loss（加入减少通讯 - 低秩正则）
    - 初始化mask
    - 根据 mask 采样一个通信图 （离散操作）
    - 用这个通信图运行 Multi-Agent 系统
    - 根据reward调整 ，如果某个随机选择出来的通信结构获得高 reward，那么提高产生它的概率。不是反向梯度更新（Policy Gradient 利用 类似 reward 来更新 mask 参数）







