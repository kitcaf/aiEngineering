# 可能问题

一、到底是学习更好的执行策略，还是在学习更准确的能力判断；这些判断又如何真正改变协作决策 

究竟在改善能力，还是改善对能力的判断？。我们应该主要抓住的点是“不是学能力，而是知道自己什么时候该做”，但是感觉跟新skill都是如何在分解能力上等问题

好像关键应该是：个体能力准确，并不意味着协作认知准确，agent 不够了解自己与别人，所以我们增加两类反思。

**第一段：问题。**

去中心化协作要求 agent 自主决定继续、委派与停止，但角色描述和历史成功经验未必能支持可靠决策。

**第二段：关键区分。**

准确认识个体能力仍不足以决定协作：伙伴的价值取决于当前进展、已有信息、失败模式及交互方式。

**第三段：研究缺口。**

因此，需要把“自己会产生什么结果”和“伙伴如何改变这些结果”连接起来，而不是分别维护能力标签和成功路由。

**第四段：方法。**

建立个体结果与条件交互效果的统一预测模型，利用对应的执行证据校准，并据此决定局部行动。

**第五段：贡献边界。**

研究在何种信息与反馈条件下，这种分解能够改善决策、复用经验，以及在哪些情况下仍会失败。


二、
(1) self-awareness: what they can and cannot do   （自我意识）
(2) social-awareness: how they should interact with others   （社会意识）

从实现来看像两套并列的经验更新

# 🔥🔥🔥🔥ICLR2027 When should An Agent Act? Aligning Capability Awareness and Social Coordination in Decentralized Multi-Agent Systems （6月把代码写完、7月跑实验）

🧠 一、核心Idea

> 智能体如何建立有证据支持的能力边界，并把这些边界转化为有效的协作决策，当各 agent 只能接触部分信息，并分别维护自己的经验时，能力判断与伙伴判断容易不一致；系统需要通过有限交互持续修正这种不一致
> 在去中心化多智能体系统中，让智能体通过执行任务的经验，逐渐认清自己的能力边界，并学会什么时候自己做、什么时候交给别人，以及交给谁 - Epistemic Calibration 

🔥 一句话版本（可以放abstract）
We propose an epistemic calibration framework for decentralized multi-agent systems, where agents iteratively align their epistemic about their own capabilities and social roles with actual performance through interaction-driven refinement, leading to more reliable and stable coordination.

👉 你做的不是“学能力”，而是：（agent 具备一定能力，但对能力适用条件认识不准确）
让agent知道自己什么时候该做、什么时候不该做，以及应该让谁来做

🧭 二、Problem Framing

🔹 核心问题定义

> 对自身能力和协作关系的判断不准确

Decentralized multi-agent systems often fail not due to insufficient capability, but due to epistemic miscalibration:

- agents act when they should not
- agents fail to defer when necessary
- agents misidentify appropriate collaborators

🔹 你的核心观点

We argue that improving capability alone is insufficient.
Instead, agents must calibrate:

(1) self-awareness: what they can and cannot do   （自我意识）
(2) social-awareness: how they should interact with others   （社会意识）


⚙️ 三、Method

3.1 System Overview

✔️ Pipeline（这是你的核心结构）
Task → Selected Agent （Planning → Executing → Verifying → Routing） → Next Agent (loop)
- Planning分析任务和目前状态，结合agent自身的skill，生成当前步骤的goal和action
- executing执行action
- Verifying检查executing的过程，例如输出格式和是否存在幻觉，若有则retry。并判断executing的结果是否满足goal
- Routing查询router graph，进而判断下一步应该交给哪个agent

需要强调的是解耦功能与verifying，均是尽量保证agent的执行无误，减少除goal-action可行性低以外的其他错误的干扰，比如LLM幻觉和输出格式错误等

👉 强调：
This structured pipeline enables explicit reasoning about both task execution and epistemic calibration.
简单介绍几种agent，但是在方法部分不需要出现它们，这是在implemetation中会介绍的
Agent的角色基于他们能使用的tool，会根据tool的能力来划分，比如
1）搜索智能体，可以调用google search、wikipedia、web（url）等工具，面向搜索任务
2）推理智能体，则tool是通过调用LLM进行逻辑推理，面向逻辑推理任务，比如math
3）代码智能体，可以写python代码，然后执行，面向计算任务或者代码实现任务
4）图像识别智能体，可以识别图片，分析内容，面向多模态

3.2 Epistemic Calibration Mechanism（核心模块）

interaction-driven refinement

✔️ 分两部分：

（1）Self-awareness Calibration（skill refinement） 自身能力校准：更新 skill （甚至可以说采用rag等技术）
常见skill模版：
例如基于当前状态分析任务，分解步骤，设定当前步骤的goal和action（包含了如何调用tool，比如对于google search而言，如何高效、精准的搜索目标信息，而对于不同目标信息，往往搜索策略也有所区别）

📌 触发点：每个Agent执行的Verifying阶段

✔️ 成功时：
- reinforce current decision pattern（结合任务当前状态，优化decision pattern，并增强共性特征，从而优化skill）
- increase confidence for similar tasks

✔️ 失败时：
- analyze failure type:
    - capability (tool) mismatch
    - missing decomposition
- revise skill（结合任务当前状态，分析和现有skill中处理的任务状态的区别，提取差异，分析为何失败。优化skill的能力边界，以及是否需要对应差异任务状态构建新的一条skill（注意都在一个skill.md文件中，新建一个条目））

👉 表达方式：
Agents update their internal decision rules based on execution outcomes and failure analysis.

并且，在routing阶段需要判断，依据目前自身的状态（skill+memory等），是继续执行优化，还是交给其他agent进行下一步处理


（2）Social-awareness Calibration（routing refinement） 协作认知校准：更新 routing （逐渐形成更合理的智能体协作方式）
 
📌 触发点：任务完成后，从全局分析routing是否合理，是否需要优化

✔️ 成功时：
- extract effective agent interaction chain（去除失败的冗余，提取成功的经验，增强共性特征，同时记录每步的任务状态等）
- generalize into routing patterns
- store in router graph

✔️ 失败时：
- identify incorrect transitions（对比现有的成功的转移，分析差异，划清边界）
- revise or remove ineffective routing patterns


3.3 Router Graph 

✔️ 数据结构
Router Graph consists of:

1. Task-level routing:
   maps task scenarios → initial agent

2. Agent-level transitions:
   maps (Agent A → Agent B) → state descriptions


✔️ 使用方式

初始化：
Retrieve candidate execution chains from task-level routing

Routing阶段：
Given current agent A:

- retrieve all candidate transitions from A
- compare stored state descriptions with current state
- select the most appropriate next agent

👉 强调：
Routing decisions are based on structured experience,
rather than fixed rules or learned parameters.


3.4 Memory Design

✔️ Agent Memory（私有）
- execution traces
- verification results
- self-reflection

✔️ System Memory（共享）
- summarized interaction states
- routing decisions

👉 一句关键话（很加分）：
Agents do not access each other's internal reasoning,
but coordinate through shared, structured interaction states.


🧪 四、Reinement

iterative refinement across task batches
上面的对于self awareness和social awareness的更新，采用batch，即同时执行，每一步执行之后，搜集优化更新各个Agent skill和router graph的意见。然后再进一步分析。对于成功的样本，则提共性特征，更新给目标skill或router graph。对于失败的样本，则对比其与目前skill或router graph的差异信息，然后利用这些差异信息来收缩边界。

初步设计是并行跑batchsize个任务，每个Agent执行后，会讲分析结果入队列，队列长度也等于batchsize。当队满的时候，则进行一次整体的skill的更新。一个batch中所有的任务都跑完的时候，会更新router graph。

✔️ 过程：
For each batch of tasks:

1. Execute tasks using current pipeline
2. Collect execution outcomes
3. Update:
    - agent skills (self-awareness)
    - routing graph (social-awareness)


📊 五、Evaluation （认知校准、协作质量、稳定性）

✔️ 你要验证的是：

1️⃣ Epistemic calibration
Do agents make better decisions about when to act?

2️⃣ Coordination quality
Do agents collaborate more effectively?

3️⃣ Stability
Does the system converge to consistent behavior?


🔥 六、贡献重写（非常关键）

1. We identify epistemic miscalibration as a key bottleneck in decentralized MAS.

2. We propose a structured pipeline that explicitly separates
   execution and calibration processes.

3. We introduce an interaction-driven refinement mechanism
   to align agents’ self- and social-awareness with actual performance.

4. We demonstrate that this leads to improved reliability,
   reduced failure rates, and emergent coordination.


👉 你整篇paper的核心定位应该是：
This is a new perspective: epistemic calibration for decentralized multi-agent systems,
with a simple but effective refinement mechanism.

🔚 总结一句话
We move from improving what agents can do,
to improving what agents know about what they can do,
and show that this leads to more stable and effective coordination.


通过对skill的优化，确定能力边界。
你的skill是拍脑袋一想，偶尔成功，还是真正的映射了agent的能力？


Baseline：
Decentralized MAS
AFLOW
AgentNet
CoMAS