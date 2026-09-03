# 可用于构建富信息传播溯源的数据集清单

> 本文件只介绍数据集：数据里有什么、从哪里获取、能否整理为“全局图 + 终态感染节点集合 + 节点/内容属性 + 真实源点”的传播溯源样本。不展开方法和实验设计。
>
> 数据中的时间戳和完整传播记录可用于识别级联、确定源点和生成终态标签；它们不必作为模型输入。核对日期：2026-08-24。


 有边的地方没有演化，有演化的地方大部分都没有边。这两件事是同一个动作的两面——用按钮就不会改，要改就得手动，手动就没有边

 我们的这个idea需要的"关系图完整 +
  内容演化密集"，在任何单一真实数据集上都不可能同时高。这不是数据集作者偷懒，是传播行为本身的结构

 不是我们找得不够，是这个组合在真实世界里稀缺
 
## 一、优先结论

上一版遗漏 Weibo 是错误的。Weibo 传播数据不仅可以使用，而且应当放在清单最前面。需要区分的是两种“全局图”口径：

- **严格社交图**：数据直接提供全局 follower/following graph；
- **全局交互图**：数据没有 follower graph，但可以把全部级联的 repost/comment/reply 边汇总成跨级联全局图。

| 推荐层级 | 数据集 | 现成传播记录与源点 | 内容/用户信息 | 可用的全局图 | 获取方式 |
|---|---|---|---|---|---|
| 第一层：富信息主数据 | Multi-domain False News on Weibo（Sheng et al.） | 44,728 个原帖级联，原帖作者即源点，约 340 万次转发 | 原帖/转发文本、时间、转发列表；原帖与转发用户画像 | 汇总全部转发树得到全局交互图 | 申请后下载 |
| 第一层：完全公开主数据 | CHECKED | 2,104 个原帖级联，原帖作者即源点；含 186 万次转发和 118 万条评论 | 文本、图片/视频、真假标签、转发/评论文本与用户 ID | 汇总转发和评论关系得到全局交互图 | GitHub 直接下载 |
| 第一层：严格社交图主数据 | Bluesky Social Dataset v3 | 可由 reply/repost/quote 及根帖恢复大量不同源点的传播对象 | 全量帖子文本与交互元数据 | 直接提供全局关注图 | Zenodo 直接下载 |
| 第二层：快速原型数据 | MILD-Weibo | 论文含 1,910 个级联；GitHub 富信息子集含 522 个级联 | 原帖、评论/转发文本、动作、时间和 13 类用户画像 | 直接提供全局关注图 | GitHub 直接下载 |
| 第二层：源定位对齐数据 | NFSL / DSLF / CRSLL Weibo | 已被直接用于真实级联源定位 | 用户画像；CRSLL 版本含评论语义 | 直接提供大规模社交关系图 | Dropbox；完整增强版可能需联系作者 |
| 第二层：多源结构数据 | Wb-MSF | 单源和多源转发级联，显式记录被转发帖子与用户 | 无公开文本和详细用户画像 | 直接提供全局关注图 | GitHub Release 直接下载 |
| 第三层：大规模传播补充 | Weibo-COV 2.0 | 可通过 `origin_weibo` 聚合转发级联 | 6,517 万条微博文本、用户 ID、地理和交互计数 | 可汇总为转发交互图 | 项目页面申请/下载 |

## 二、Weibo 传播数据集

### 1. Multi-domain False News on Weibo（Sheng et al., 2022）

- **规模与范围**：2009—2019 年，九个主题领域，44,728 条虚假信息原帖，由 40,215 个发布者发出，累计约 340 万次转发。
- **传播信息**：每条原帖对应一个 cascade；包含原帖、转发列表和发布时间。原始转发文本中的 `//@用户` 链可被解析为传播树。
- **内容信息**：原帖内容和每次转发时附加的文本。
- **用户信息**：论文说明对每个原帖用户和转发用户采集了用户画像，重点字段包括性别、年龄和认证状态。
- **源点标签**：每条原帖的发布用户是已知源点；不同级联具有大量不同发布者，不会像单一媒体账号数据那样把源点退化为常数标签。
- **全局图**：未公开独立的全量 follower graph；可以把全部 cascade tree 的转发边汇总为跨级联的全局用户交互图。
- **数据来源**：[GitHub、数据申请表与分析代码](https://github.com/ICTMCG/Characterizing-Weibo-Multi-Domain-False-News)
- **本地论文**：[037_Characterizing_Multi-Domain_False_News_and_Underlying_User_Effects_on_Chinese_Weibo.pdf](<../ref/pdf/037_Characterizing_Multi-Domain_False_News_and_Underlying_User_Effects_on_Chinese_Weibo.pdf>)

### 2. CHECKED

- **规模与范围**：2,104 条经过核验的 COVID-19 微博，其中 344 条虚假、1,760 条真实；包含 1,868,175 次转发、1,185,702 条评论和 56,852,736 次点赞。
- **传播信息**：每条根微博保存详细 `reposts` 和 `comments`，其中有转发者/评论者匿名 ID、时间及相应文本；根微博作者即源点。
- **内容信息**：根帖文本、图片 URL、视频 URL、真假标签；虚假微博还提供专家分析与判定理由。
- **用户信息**：根帖作者、转发者和评论者均使用稳定的匿名用户 ID，可跨记录联结。
- **全局图**：不提供独立 follower graph；可把所有 repost/comment 用户关系汇总为全局交互图。
- **数据来源**：[GitHub 数据文件与字段说明](https://github.com/cyang03/CHECKED)
- **本地论文**：[036_CHECKED_Chinese_COVID-19_Fake_News_Dataset.pdf](<../ref/pdf/036_CHECKED_Chinese_COVID-19_Fake_News_Dataset.pdf>)

### 3. MILD News / MILD-Weibo

这正是论文《Make Information Diffusion Explainable》中使用并在 GitHub 发布的数据。它没有理由被排除。

| 字段 | News | Weibo |
|---|---:|---:|
| 用户数 | 10,255 | 31,061 |
| 全局社交边 | 83,959 | 294,577 |
| 论文中的级联数 | 1,291 | 1,910 |
| GitHub `filtered_tweets.json` 富信息级联数 | 180 | 522 |
| 富信息子集中的不同原始源用户数 | 1 | 4 |

- **传播信息**：参与用户顺序、行为类型、精确时间戳、评论/转发文本和原帖内容。
- **用户信息**：全局有向关注图以及 13 类画像字段，包括粉丝数、关注数、认证状态、地区、性别、账号创建时间、发帖数和个人描述等。
- **仓库文件**：`graph.txt`、`filtered_tweets.json`、`augmented_user_profile.json`、`cooccurance_edges.txt`、`gt.json` 和 LLM 推断得到的传播影响边。
- **News 来源结构**：级联都由同一官方媒体账号发起。
- **Weibo 来源结构**：论文数据来自五位意见领袖；当前 GitHub 富信息子集中实际出现四个不同原始源用户，而且分布明显不均衡。
- **数据来源**：[MILD 官方 GitHub](https://github.com/Shang-hub/MILD-Official-Implementation)
- **本地论文**：[017_Make Information Diffusion Explainable LLM-based.pdf](<../ref/pdf/017_Make Information Diffusion Explainable LLM-based.pdf>)

因此，MILD-Weibo 的字段完整度很高，适合验证数据管线和语义建模；但它的源点身份数太少，不能单独充当一个有说服力的源定位主数据集。MILD-News 的真实源点只有一个，更不能把“识别原帖发布者”直接作为源定位任务。

### 4. NFSL / DSLF / CRSLL 使用的 Weibo 数据

这三项工作使用的是同一条真实级联源定位数据路线，最适合保留为与已有 source-localization 文献直接对齐的数据来源。

- **基础规模**：2,856,741 个用户、3,508,596 条社交关系、4,664 个级联。
- **传播信息**：真实传播级联和源点标签；NFSL 同时讨论 sensor-based 与 snapshot-based 输入，使用时只保留当前研究需要的终态快照版本。
- **用户信息**：NFSL 版本给用户补充六个画像维度；CRSLL 进一步使用用户画像与评论文本，论文报告 Weibo 部分包含 61,247 条评论。
- **全局图**：现成的大规模用户社交关系图。
- **数据可得性**：GitHub 提供代码和公开 Dropbox 入口；仓库同时说明受平台政策和文件体积限制，完整原始/增强数据可能需要联系作者。
- **代码与入口**：[NFSL](https://github.com/cgao-comp/NFSL)、[DSLF](https://github.com/cgao-comp/DSLF)、[CRSLL](https://github.com/cgao-comp/CRSLL)
- **本地论文**：[020_NFSL.pdf](<../ref/pdf/020_NFSL_New_Localization_Frameworks_User-centric_Approaches_to_Source_Localization_in_Real-world_Propagation_Scenarios.pdf>)、[021_DSLF.pdf](<../ref/pdf/021_DSLF_Joint_Source_Localization_in_Different_Platforms_via_Implicit_Propagation_Characteristics_of_Similar_Topics.pdf>)、[011_CRSLL.pdf](<../ref/pdf/011_Good Advisor for Source Localization Using Large Language Model.pdf>)

### 5. Wb-MSF

- **数据类型**：Weibo 单源传播（SSC）和多源传播（MSC）数据。
- **传播字段**：每次记录包含 `origin_id`、`origin_uid`、当前帖子 `id`、当前用户 `uid` 和 `created_at`；可直接恢复谁转发了谁以及一个级联中的一个或多个源点。
- **全局图**：每个子数据集都提供全局 followership edge list。
- **匿名化**：用户 ID 和帖子 ID 已哈希。
- **缺失信息**：公开字段不含帖子文本、评论语义和详细用户画像，因此它是多源/结构溯源数据，而不是富语义主数据。
- **数据来源**：[GitHub、1K 子集与完整版本下载](https://github.com/Les1ie/Wb-MSF)

### 6. Weibo-COV 2.0

- **规模与时间**：2019-12-01 至 2020-12-30，共 65,175,112 条关键词过滤微博，并发布匿名化的 2,000 万活跃用户池。
- **主要字段**：微博 ID、匿名用户 ID、发布时间、正文、点赞/转发/评论数、`origin_weibo` 和地理信息。
- **传播信息**：可以利用 `origin_weibo` 将原帖与转发帖聚合为级联，并以原帖作者作为源点。
- **全局图**：无独立 follower graph；可汇总所有 `user_id → origin_user_id` 关系形成全局转发交互图。
- **数据来源**：[Weibo 公共舆情数据集项目](https://github.com/xianlimei/weibo-public-opinion-datasets)

### 7. SocialNet-Weibo V1 / V2

- **V1**：官方 README 标称 2,106 条新闻，同时列出 1,000 条虚假和 1,067 条真实（两项合计为 2,067，原始说明本身存在数量不一致）；包含用户、评论与多模态信息。
- **V2**：11,329 条新闻，包含根帖、评论集合、图片、视频和语音。
- **可用信息**：适合提供根帖语义、回复语义和多模态属性。
- **缺失信息**：官方说明没有给出现成 follower graph，也没有明确保证完整的 repost parent-child 级联，因此不能只靠该数据完成严格的传播溯源构造。
- **数据来源**：[GitHub 字段说明](https://github.com/yzhouli/SocialNet/blob/master/Weibo/README.md)、[V1 Kaggle](https://www.kaggle.com/datasets/yangzhou32/socialnet-weibo-version-1)、[V2 Kaggle](https://www.kaggle.com/datasets/yangzhou32/socialnet-weibo-version-2)

### 8. MDFEND-Weibo21：不要与上面的传播数据混淆

- **规模**：9,128 条中文真假新闻，覆盖九个领域；含 4,488 条虚假和 4,640 条真实样本。
- **主要信息**：根帖文本、图片、真假标签和领域标签。
- **关键缺失**：其公开任务是多领域假新闻检测，仓库并没有随每条根帖发布完整的转发级联和全局社交图。
- **数据来源**：[MDFEND-Weibo21 GitHub](https://github.com/kennqiang/MDFEND-Weibo21)

Weibo21 可以补充内容和领域标签，但不能单独被当作已经可转换的传播溯源数据。它也不是 Sheng et al. 的 44,728-cascade 多领域 Weibo 传播数据。

## 三、其他平台上的富信息数据

| 数据集 | 平台 | 可用传播/交互信息 | 内容与属性 | 全局图 | 数据来源 |
|---|---|---|---|---|---|
| Bluesky Social Dataset v3 | Bluesky | reply、repost、quote、thread 与时间 | 约 2.35 亿帖子、用户和 feed 信息 | 现成关注图 | [Zenodo](https://zenodo.org/records/14669616) |
| MADOC | Bluesky、Koo、Reddit、Voat | post/comment/repost、`parent_id`、`parent_user_id` | 文本、URL、语言、情感和毒性分数 | 可汇总交互图 | [Zenodo](https://zenodo.org/records/15690964) |
| Reddit Politosphere | Reddit | 评论链与用户交互 | 评论文本、社区和用户元数据 | 现成用户交互网络 | [Zenodo](https://zenodo.org/records/5851729) |
| FediData | Mastodon | 每个用户近期帖子 | 用户画像、帖子文本、图片 | 现成 follower/following 图；缺少完整级联 | [Zenodo](https://zenodo.org/records/15621244) |
| POLITISKY24 | Bluesky | like、repost、quote | 用户发帖历史、立场、解释和证据文本 | 互动图；非完整关注图 | [Zenodo](https://zenodo.org/records/15616911) |
| MemeTracker | 新闻与博客 | 相同短语在站点间出现的时间和网页链接 | 短语/文本片段、URL | 网页超链接图 | [SNAP](https://snap.stanford.edu/data/memetracker9.html) |

其中，**Bluesky Social Dataset v3** 是严格满足“多源传播对象 + 帖子语义 + 全局关注图”的最完整公开候选；它的代价只是需要从 reply/repost/quote 记录中自行生成级联与终态感染集合。其余数据更适合作为跨平台或特定属性的补充来源。

## 四、按当前任务口径推荐的数据集组

### A. 如果“全体社交图”必须是 follower/following graph

1. **Bluesky Social Dataset v3**：多源、文本丰富、关注图和交互同时公开；最适合成为新构建数据的主体。
2. **MILD-Weibo**：字段最贴近 LLM + profile + comments，但只作为小规模富信息数据。
3. **NFSL / DSLF / CRSLL Weibo**：保留与现有真实传播溯源工作的直接可比性。
4. **Wb-MSF**：补充多源传播，但没有文本和画像。

### B. 如果允许“全体社交图”使用跨级联聚合的交互图

1. **Multi-domain False News on Weibo（Sheng et al.）**：源点多、领域多、传播树大、同时具有转发文本和用户画像，是最值得优先申请的 Weibo 数据。
2. **CHECKED**：完全公开，转发和评论数量大，文本及多模态字段完整；主题集中在 COVID-19。
3. **Weibo-COV 2.0**：规模最大，文本与 `origin_weibo` 可用于生成大量级联；画像与精确 parent-child 信息较弱。
4. **MADOC / Reddit Politosphere**：作为非 Weibo 的跨平台富信息补充。

---

## 五、内容溯源 / 文本复用类数据集（另一类候选）

> 前四节的数据集都以 **repost 树为采样单位**：一个事件从一个原帖出发，抓它的转发副本。
> 这类采样在设计上只保留了「同一 claim 的转发副本」，天然缺少「同一 claim 的**独立编辑版本**」，
> 因而复用关系充分、但**语义级内容演化关系（改写、翻译、去归因、裁剪）稀薄**
> （详见 `dataAnalyze/R01_REPORT.md`:内容演化的 58% 信号几乎全部来自 `//@` 链这种**结构性**演化，
> 而非语义级编辑)。
>
> `idea/01_idea.md` 的核心需要的是「**同一 claim 的多个独立编辑版本在终态中共存**」。
> 满足这一点的是学术上称为 **text reuse / content provenance** 的一支：它们的采样单位天然是
> **内容簇（同一内容的所有变体）** 而不是 cascade，正好补齐 repost 树数据缺失的「语义演化」维度。
>
> ⚠️ 取舍说明:这类数据集的「关系图」不再是社交关注图,而是**站点/频道/媒体间的关系图**
> (超链接、转载、转发、共引)。**若某个内容溯源场景无法满足核心 idea 对「关系网络 + 感染分布」的需求,
> 则可以放弃关系图这一约束,把该数据集仅作为「纯内容侧的方向性验证」使用**
> (对应 R01 §4.3 的结论:内容方向信号在图上不相邻甚至无图的节点对之间同样成立)。

### 判据:一个传播/内容数据集的「内容状态」是否合理

取决于一个问题:**每个节点承载的 claim 载荷,彼此之间是不是同一份内容的不同版本?**

| 类型 | 节点承载内容 | 复用/演化 | 例子 | 合理性 |
|---|---|---|---|---|
| A. repost 树采样 | 同一 claim 的转发链 | 复用充分;演化仅**结构性**(`//@`链增删/截断) | MILD-Weibo、CHECKED、Sheng-Weibo | 半合理 |
| B. 内容簇采样 | 同一 claim 的**独立变体** | 复用 + **语义级演化**均充分 | MemeTracker、Viral Texts、Quotebank、TGDataset | 完全合理 |
| C. 独立原帖 | 各说各的 | 几乎无关 | 假新闻检测集(Weibo21) | 不合理 |

### 1. TGDataset（Telegram,首选社交侧内容簇数据）

- **规模**：120,979 个公开频道,498,320,597 条消息(4 个压缩包,121 个 JSON 文件)。
- **关键字段(每条消息)**：`message`、`date`、`author`、`is_forwarded`、`forwarded_from_id`、`forwarded_message_date`;频道级含 `n_subscribers`、`verified`、`scam` 等。
- **为什么最贴合 idea**:
  - `is_forwarded=true` + `forwarded_from_id` → 平台记录的显式传播边 = **内容复用**(原文逐字保留);
  - `is_forwarded=false` 但内容与他处高度相似 → **copy-without-link** = **内容演化**(手动重发时的删改/翻译/去归因)。
  - **即「复用 vs 演化」的二分是数据集自带的布尔字段,无需 LLM 判定,规避 R01 §1.2 的循环论证。**
  - 频道转发不带评论,节点内容即完整 claim 载荷,**不存在「这很好」这类空转发噪声**。
- **关系图**：把全库 `forwarded_from_id` 聚合成频道→频道有向图(12 万节点)。
- **时间**：`date` + `forwarded_message_date`(原帖时间),源区域标签可靠。
- **代价**：数据大需筛选;claim 级聚类需自建(近似去重 + 时间窗);节点粒度为**频道**而非个人。
- **数据来源**：[Zenodo 记录](https://zenodo.org/records/7640712)、[GitHub SystemsLab-Sapienza/TGDataset](https://github.com/SystemsLab-Sapienza/TGDataset)

### 2. MemeTracker（新闻/博客,最经典的内容溯源基准）

- **规模**：约 90 万个 phrase cluster,追踪同一 quote 在新闻站与博客间的出现与变体。
- **为什么合适**:
  - **现成的 phrase cluster** = 现成的内容簇,作者本来就是为追踪同一 quote 的变体而设计;
  - 簇内既有逐字相同(复用)也有截断/改写变体(演化)——**内容演化是数据集的设计目标本身**;
  - 大量站点转载**不加链接**,是 copy-without-link 机制的直接实例。
- **关系图**：站点/博客间的**超链接图**;**时间**:精确到小时。
- **论文价值**：Leskovec KDD'09 经典,数据合法性无需解释,可信度高。
- **代价**：2008–2009 年、英文、节点是站点非用户、超链接图非社交图(在「关系图即可」框架下不构成硬伤)。
- **数据来源**：[SNAP MemeTracker](https://snap.stanford.edu/data/memetracker9.html)

### 3. Viral Texts / Oceanic Exchanges（19 世纪报纸转载,内容演化密度最高）

- **本质**：同一篇文章在数百份报纸间被转载,**自动检测出的 reprint 簇**是数据集核心产物。
- **为什么合适**：转载时的**截断、删节、去掉原报社署名**被历史学界详细记录——正是 idea 说的「出处移除」「局部裁剪」,且真实样本量大。
- **关系图**：报纸间转载关系网络;**时间**:出版日期。
- **代价**：非社交媒体、19 世纪英文;数据获取与格式偏学院派,需摸索。**候选中内容演化密度最高**。
- **数据来源**：[Viral Texts Project](https://viraltexts.org/)、[Oceanic Exchanges](https://oceanicexchanges.org/)

### 4. Quotebank（跨新闻媒体引语,最贴「重新归因」）

- **规模**：约 1.78 亿条引语,跨大量新闻媒体。
- **为什么合适**：同一句引语在不同媒体的**归因变化**(说话人被改、被删)是数据集直接建模的对象,精确对应 idea 的「重新归因」。
- **关系图**：媒体间关系;**时间**:齐全。
- **代价**：以引语为中心,claim 边界较细;需将引语聚合为事件。
- **数据来源**：[Quotebank(Vaucher et al., WSDM 2021)](https://zenodo.org/records/4277311)

### 5. Wikipedia 修订历史 / 跨语言版本（编辑 ground truth 最完整）

- **本质**：单文档的完整编辑 DAG(增删改有真实时序),跨语言版本天然提供翻译型演化。
- **为什么合适**：编辑操作有**完整 ground truth**,可用于校准「编辑不对称性」的度量。
- **代价**：**传播网络薄弱**,更像单文档演化而非跨节点传播;更适合作为「编辑算子不对称性」的机制验证,而非源定位主数据。
- **数据来源**：[Wikimedia dumps](https://dumps.wikimedia.org/)

### 6. MADOC（多社区聚合,非「跨平台」定位)

- **本质**：Bluesky / Koo / Reddit / Voat 四个在线社区的同主题聚合语料,含 `parent_id`/`parent_user_id`、文本、URL、语言、情感/毒性分数。
- **说明**：**注意 MADOC ≠ Bluesky Social Dataset v3**(两个不同 Zenodo 记录)。本地 `data/MADOC-Bluesky/bluesky_madoc.parquet.part` 仅为 MADOC 的 Bluesky 分区,未下完。
- **定位**：作为「松耦合在线社区」的补充内容簇数据,不强调平台维度。claim 级跨社区重叠量**未知,需先测**。
- **数据来源**：[Zenodo 15690964](https://zenodo.org/records/15690964)

### 推荐的两数据集组合(出论文最小集)

| 组合 | 主数据 | 第二数据 | 优点 | 风险 |
|---|---|---|---|---|
| **甲(推荐)** | **TGDataset** | **MemeTracker** | 一社交 + 一新闻;`is_forwarded` 免费给复用/演化标注;MemeTracker 提供经典可信度 | 工程量中等,需自建 claim 聚类 |
| 乙 | TGDataset | Viral Texts | 内容演化密度最高 | Viral Texts 获取/格式偏学院派 |
| 丙(保守) | MILD-Weibo(重定义为 claim 载荷) | MemeTracker | 复用现有管线,最快 | 微博语义级演化稀薄,可能撑不起主实验 |

最稳妥的清单不是删掉 Weibo，而是把 **Sheng-Weibo、CHECKED、MILD-Weibo、NFSL/CRSLL-Weibo 和 Wb-MSF** 分别放在“富语义主数据、开源主数据、快速原型、直接源定位对齐、多源结构补充”这五个位置；Bluesky 则用于补足“多源 + 文本 + 完整关注图”这一目前公开 Weibo 数据较难同时满足的组合。
