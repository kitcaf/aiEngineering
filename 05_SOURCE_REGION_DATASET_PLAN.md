# ForeTrace / SemTrace 论文数据集选择方案

> 更新日期：2026-08-26  
> 对应研究设定：[03_idea.md](./03_idea.md)  
> 数据集背景清单：[04_RECOMMENDED_RICH_DATASET_BLUESKY.md](./04_RECOMMENDED_RICH_DATASET_BLUESKY.md)

## 一、研究任务对数据集的要求

本文研究终态传播源区域定位。对于传播事件 \(c\)，模型能够观察：

\[
\mathcal O_c=
\left(
G_c,\,
I_c^T,\,
X_c^T
\right),
\]

其中：

- \(G_c\) 是社交关系网络或事件发生前的历史交互网络；
- \(I_c^T\) 是终态时刻的无序感染节点集合；
- \(X_c^T=\{x_{u,c}^T:u\in I_c^T\}\) 是感染节点最终保留或发布的内容状态。

时间、感染顺序、显式传播树和真实传播路径仅用于构造数据与监督标签，不作为模型输入。

适合本文的数据集应尽量提供：

1. 可构造关系网络的用户关系或跨事件交互记录；
2. 可按传播事件划分的参与用户集合；
3. 每个参与用户对应的事件相关内容；
4. 用户参与事件的时间；
5. 可跨记录稳定连接的用户标识。

## 二、最早 5% 感染节点源区域

本文不把平台记录中的第一个帖子作者直接等同于唯一真实传播源，而将传播早期的一组感染节点定义为**源区域（source region）**。

设节点 \(u\) 首次参与事件 \(c\) 的时间为 \(t_{u,c}\)。对所有感染节点的首次参与时间计算经验 5% 分位数：

\[
\tau_c^{(5\%)}=
Q_{0.05}
\left(
\{t_{u,c}:u\in I_c^T\}
\right).
\]

源区域定义为：

\[
S_c^{(5\%)}=
\left\{
u\in I_c^T:
t_{u,c}\leq\tau_c^{(5\%)}
\right\}.
\]

相同时间戳产生的并列节点全部保留，因此源区域节点数可能略大于感染节点总数的 5%。

工程实现可以令：

\[
k_c=\max\left(1,\left\lceil0.05|I_c^T|\right\rceil\right),
\]

先选取首次感染时间最早的 \(k_c\) 个节点，再纳入与第 \(k_c\) 个节点时间相同的全部并列节点。

主实验使用 5%，并建议报告：

\[
\alpha\in\{1\%,3\%,5\%,10\%\}
\]

的敏感性结果。

## 三、数据集当前下载状态

| 数据集 | 当前状态 |
|---|---|
| MILD-Weibo | 已下载：`../data/Weibo/` |
| CHECKED | 已下载：`../data/CHECKED/`；论文主要使用 `fake_news` |
| MADOC-Bluesky | 正在手动下载；目标文件为 `../data/MADOC-Bluesky/bluesky_madoc.parquet` |
| MILD-News | 已下载：`../data/News/`，但不纳入核心数据组合 |
| Sheng-Weibo | 仅代码和说明已下载：`../data/Sheng-Weibo/`；原始数据尚未取得 |
| Bluesky Social Dataset v3 | 尚未下载 |

## 四、核心候选数据集说明

### 4.1 MILD-Weibo

MILD-Weibo 已提供：

- 全局社交图；
- 传播级联参与用户；
- 用户参与时间；
- 原帖和参与用户内容；
- 用户画像。

本地富内容部分包含 522 个级联、31,061 个用户和 109,027 条参与文本。虽然原帖作者集中在四个用户，但按最早 5% 感染时间构造源区域后，约有 4,444 个不同用户曾进入源区域。

因此，MILD-Weibo 能够直接构造：

\[
G_c+I_c^T+X_c^T\rightarrow S_c^{(5\%)}.
\]

### 4.2 CHECKED-Fake

CHECKED-Fake 包括：

- 344 个虚假信息事件；
- 338 个不同根帖作者；
- 215 个具有转发记录的事件；
- 40,358 条转发文本；
- 完整的根帖和转发时间；
- 可稳定连接的转发用户 ID。

CHECKED-Real 不纳入主要实验，因为其根帖作者单一，而且绝大多数转发记录缺少有效用户 ID。

### 4.3 MADOC-Bluesky

MADOC-Bluesky 文件约 455 MB，主要字段包括：

- `post_id`
- `publish_date`
- `user_id`
- `parent_id`
- `parent_user_id`
- `content`
- `interaction_type`
- `platform`

它能够使用历史 reply、comment 和 repost 构建关系网络，并利用用户、时间和内容构造终态感染集合及最早 5% 源区域。

下载地址：

- [直接下载 bluesky_madoc.parquet](https://zenodo.org/records/15690964/files/bluesky_madoc.parquet?download=1)
- [MADOC Zenodo 数据页面](https://zenodo.org/records/15690964)

下载完成后保存为：

```text
../data/MADOC-Bluesky/bluesky_madoc.parquet
```

### 4.4 Sheng-Weibo

Sheng-Weibo 完整数据包括：

- 44,728 个原帖级联；
- 40,215 个原帖发布者；
- 约 340 万次转发；
- 原帖和转发文本；
- 发布时间和用户信息；
- 可解析的多层转发结构。

当前尚未取得完整数据。官方仓库早期公开过以下 Google Drive 地址：

- [Sheng-Weibo 旧 Google Drive 链接](https://drive.google.com/file/d/1JhyLmAd3hcAnhB4LlX3hLBL1VsBhwaOL/view)

该文件约 311 MB，目前可能需要登录或申请访问。

## 五、最推荐的统一实验数据集

本文不再区分预备实验数据与正式实验数据。方法开发、基线比较、消融实验和最终结果原则上使用同一组数据集。

### 5.1 如果 Sheng-Weibo 没有拿到

建议整篇论文统一使用三个数据集。

#### 1. MILD-Weibo

- 已下载；
- 有社交图、感染节点、内容和时间；
- 作为主要中文数据。

#### 2. CHECKED-Fake

- 已下载；
- 作为公开中文辅助数据；
- 不使用用户 ID 大量缺失的 CHECKED-Real。

#### 3. MADOC-Bluesky

- 文件约 455 MB；
- 作为英文跨平台数据；
- 使用事件发生前的历史交互图代替 follower graph。

这是当前最现实、统一且可复现的三数据集方案：

\[
\boxed{
\text{MILD-Weibo}
+
\text{CHECKED-Fake}
+
\text{MADOC-Bluesky}
}
\]

### 5.2 如果后续拿到 Sheng-Weibo

统一使用四个数据集：

1. **Sheng-Weibo**
2. **MILD-Weibo**
3. **CHECKED-Fake**
4. **MADOC-Bluesky 或完整 Bluesky Social Dataset v3**

其中 MADOC-Bluesky 和完整 Bluesky v3 二选一，因为它们的数据来源重叠，不能作为两个相互独立的数据集。

对应组合为：

\[
\boxed{
\text{Sheng-Weibo}
+
\text{MILD-Weibo}
+
\text{CHECKED-Fake}
+
\text{MADOC-Bluesky / Bluesky v3}
}
\]

如果希望将最终数据集数量控制为三个，可以使用：

1. Sheng-Weibo；
2. MILD-Weibo；
3. MADOC-Bluesky 或完整 Bluesky v3。

此时 CHECKED-Fake 作为补充结果，不进入全部主要结果表。
