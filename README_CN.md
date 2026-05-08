# omo-vs-opencode-benchmark

[English Version](./README.md)

## 项目简介

这是一个对比 `oh-my-opencode`（omo）与裸 `OpenCode` 在研究型任务上表现的实验仓库。仓库记录了 3 组真实测试结果，重点观察在相同模型与相同任务条件下，不同 agent 组织方式对 token 消耗、关键事实捕获能力、来源质量和最终结论可用性的影响。

## 核心结论

`裸 OpenCode + Librarian 提示词` 并没有参与全部 3 轮测试，而是只参与了后两轮。但在已参与的 `T2`、`T3` 中，它的研究结论质量均与 `omo` 持平或更优，同时 token 消耗仅为 `omo` 的约 `1/6` 到 `1/2`。

另外，旧版裸 OpenCode 虽然在 `T2` 中 token 很低，但遗漏了真正关键的隐藏事实，说明 research 任务里“低消耗”不等于“高质量”。

## 测试结论汇总

| 测试主题 | omo token 消耗 | 裸 OC token 消耗 | 谁找到了关键隐藏事实 |
| --- | ---: | ---: | --- |
| T1 Tokio vs async-std | 325k | 172k | omo |
| T2 Bun vs Node vs Deno | 335k | 30k（旧版裸 OC） / 228k（裸 OC + Librarian） | omo、裸 OC + Librarian |
| T3 Redis vs Valkey vs KeyDB | 432k | 26k（裸 OC + Librarian） | 裸 OC + Librarian |

完整测试方法见 [methodology_CN.md](./methodology_CN.md)。

三组设置的定义，以及“每次实验都会拉起 3 个 Librarian subagent”这一共同规则，也在该文件中说明。

## 关键数字

- T1: `omo 325k` vs `裸 OC 172k`
- T2: `omo 335k` vs `裸 OC 旧版 30k` vs `裸 OC + Librarian 228k`
- T3: `omo 432k` vs `裸 OC + Librarian 26k`

## 关键发现

- T1 隐藏事实：`async-std` 官方已停止维护，并明确建议改用 `smol`。
- T2 隐藏事实：`Deno LTS` 将于 `2026-04-30` 终止。
- T3 隐藏事实：`KeyDB` 创始人在 `issue #895` 中公开建议用户转向 `Valkey`。

### 命中情况

- `T1`：`omo` 找到了关键隐藏事实；`裸 OC 旧版` 未找到；`裸 OC + Librarian` 未参与。
- `T2`：`omo` 找到了 `Deno LTS 终止日期`；`裸 OC 旧版` 只发现了不够精确的 `Deno 2.0 转型`；`裸 OC + Librarian` 找到了更完整的官方战略变化信息。
- `T3`：`omo` 漏掉了 `KeyDB issue #895`；`裸 OC + Librarian` 找到了创始人公开建议转向 `Valkey` 的关键信息；`裸 OC 旧版` 未参与。

## 分轮对比

### T1: Tokio vs async-std

本轮只对比了 `omo` 和 `裸 OC 旧版`。

- `omo`：✅ 找到 `async-std` 官方已停止维护
- `裸 OC 旧版`：❌ 完全未发现
- `裸 OC + Librarian`：本轮未参与测试

### T2: Bun vs Node vs Deno

本轮三组均参与对比。

| 指标 | 裸 OpenCode（旧版） | omo | 裸 OpenCode + Librarian提示词 |
| --- | --- | --- | --- |
| 消耗 token | ~30k | ~335k | ~218-238k |
| 最终 context | 30,125 | 74,873 | 69,672 |
| 调用次数 | 1 | 6 | 4-5 |
| 一手来源数量 | 5个 | 6个 | 7个 |
| 找到 Deno LTS 具体终止日期 | ❌ | ✅ 2026-04-30 | ❌ |
| 找到 Deno 官方战略说明博文 | ❌ | ❌ | ✅ Deploy/KV策略调整 + "greatly-exaggerated"博文 |
| 排除二手文章 | ❌ 混入了 | ⚠️ 部分 | ✅ 严格执行 |

结论：

- `omo`：✅ 找到 `Deno LTS` 终止日期 `2026-04-30`
- `裸 OC 旧版`：❌ 仅发现 `Deno 2.0 转型`，但不够精确
- `裸 OC + Librarian`：✅ 找到更完整信息，包括 Deno 官方 `"greatly exaggerated"` 博文与 `Deploy/KV` 战略转向

### T3: Redis vs Valkey vs KeyDB

本轮只对比了 `omo` 和 `裸 OpenCode + Librarian提示词`。

| 指标 | omo | 裸 OpenCode + Librarian提示词 |
| --- | --- | --- |
| 消耗 token | ~432k | ~26k（16倍差距） |
| centminmod benchmark | ✅ | ✅ |
| Redis 许可证变化 | ✅ | ✅ |
| Valkey 1.2M req/s 官方数据 | ❌ | ✅ |
| KeyDB issue #895（创始人说“转移到Valkey”） | ❌ | ✅ |
| benchmark 数据详细程度 | 中等 | 更完整 |

结论：

- `omo`：❌ 漏掉 `KeyDB issue #895`
- `裸 OC + Librarian`：✅ 找到 `KeyDB` 创始人公开建议转向 `Valkey`
- `裸 OC 旧版`：本轮未参与测试

## 结果解读

这组实验说明，研究型任务的优劣不只取决于模型本身，也强烈依赖 agent 的检索策略和提示词设计。`Librarian` 风格提示词把注意力集中到官方文档、版本状态、issue/PR 历史和可核验来源后，裸 OpenCode 在高价值信息捕获上明显提升。

更准确地说：`裸 OC + Librarian` 只参与了后两轮测试，但在 `T2`、`T3` 两轮中，研究质量都与 `omo` 持平或更优，而 token 消耗仅为 `omo` 的 `1/6` 到 `1/2`。

## 局限性

- 只测试了 research 类任务，没有覆盖编码、修复 bug、重构或长链工具调用等场景。
- 所有测试都基于 `GPT-5.4`，结论不自动外推到其他模型。
- 样本量只有 `3` 次测试，适合作为观察性结论，不足以构成严格统计学证明。

## 目录结构

```text
.
├── README.md
├── README_CN.md
├── methodology.md
├── methodology_CN.md
├── prompts/
│   ├── librarian.md
│   └── librarian_CN.md
└── results/
    ├── T1-tokio-vs-async-std/
    ├── T2-bun-vs-node-vs-deno/
    └── T3-redis-vs-valkey-vs-keydb/
```

## `results/` 说明

- `results/` 已整理为原始实验结果目录。
- 本次文档调整不会修改 `results/` 中的任何文件。

## 后续用途

这个仓库适合用作：

- 公开实验记录
- agent 设计案例
- 对外分享 `OpenCode` 研究工作流经验
- 后续继续追加更多 benchmark 样本
