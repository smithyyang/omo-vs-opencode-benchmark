# Methodology

[English Version](./methodology.md)

## 测试方法

本实验目标是尽量控制变量，只比较 agent 组织方式对 research 任务结果的影响。

统一条件如下：

- 相同任务 prompt
- 相同模型：`GPT-5.4`
- 相同 MCP 配置
- 相同任务类型：研究型、资料核验型问题
- 结果以最终产出质量与 token 成本共同评估

## 测试对象

- `omo`
- `裸 OpenCode（旧版）`
- `裸 OpenCode + Librarian 提示词`

这里的 `Librarian` 指从 `oh-my-opencode` 中提取并迁移到裸 OpenCode 的研究型检索提示词。

### 三组设置的具体含义

- `omo`：直接使用 `oh-my-opencode` 执行任务。
- `裸 OpenCode（旧版）`：使用裸 `OpenCode`，并配一个普通的、非 omo 来源的 Librarian 风格提示词。
- `裸 OpenCode + Librarian 提示词`：使用裸 `OpenCode`，但其中的 Librarian agent 明确配置为采用从 `oh-my-opencode` 提取出来的 Librarian 提示词。

### Librarian 拉取方式

所有实验都保持同一类高层检索结构：每次研究任务都会拉起 `3 个 Librarian subagent` 参与测试。

因此，这里的比较并不是绝对意义上的“有 Librarian”对“没 Librarian”，而是比较：

- `omo` 自带 agent 体系下的表现
- `裸 OpenCode` 搭配普通 Librarian 风格提示词的表现
- `裸 OpenCode` 搭配 `omo` 的 Librarian 提示词后的表现

核心关注点也因此更明确：Librarian 提示词设计与 agent 编排方式，究竟会怎样影响 token 消耗、关键隐藏事实捕获、来源质量以及最终结论质量。

## token 计算方式

token 统计方式不是简单把某一列“Token 总数”直接相加，而是按每次调用的真实成本累计：

- 输入 token
- 输出 token
- 推理 token

也就是说，最终展示的 token 消耗是基于一次完整任务执行过程中每轮调用的 `input + output + reasoning` 汇总，而不是直接抄录某个面板上的单列总数。

## 评分维度

本仓库主要从以下 4 个维度评估结果：

1. `token 消耗`
2. `关键隐藏事实捕获`
3. `来源质量`
4. `结论可用性`

### 1. token 消耗

关注完成一次研究任务需要付出的总 token 成本，而不是只看回答是否更长或更短。

### 2. 关键隐藏事实捕获

重点观察系统是否找到了真正改变判断结论的事实，例如：

- 项目已经停止维护
- 官方 LTS / 支持策略即将终止
- 创始人或核心维护者公开建议迁移到替代项目

这类信息通常不会出现在表层 benchmark 对比中，但会直接改变研究结论是否可靠。

### 3. 来源质量

优先级从高到低通常为：

- 官方文档
- 官方 issue / PR / release / roadmap
- 项目仓库中的可核验说明
- 有方法学和原始数据的独立 benchmark
- 二手文章或 SEO 汇总（最低优先级）

### 4. 结论可用性

判断最终回答是否真的能支持技术选型，而不只是堆砌资料。可用结论通常应满足：

- 能给出明确方向
- 能指出适用前提
- 能解释风险与边界
- 能让读者据此继续验证

## 解释原则

本实验不把“token 越少”直接等同于“结果越好”。

- 如果 token 很低，但遗漏关键隐藏事实，那么结果仍然可能误导决策。
- 如果 token 略高，但显著提升了关键事实命中率与来源质量，这种额外消耗在研究任务中通常是值得的。

因此，本仓库的核心关注点是：`单位 token 成本下，谁更容易产出可用于决策的研究结论。`
