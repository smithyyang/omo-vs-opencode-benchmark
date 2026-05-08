I read this as scoped-general research completed: produce a 2025 production comparison from primary materials, not SEO summaries. Below is a source-disciplined comparison; I only used original announcements, official project pages, official repo artifacts, or reproducible benchmark repos with published methodology, and I explicitly separate official first-party data from independent reproducible tests.
先说结论
- 如果你最在意“许可证稳定 + 社区治理 + 2025 公开活跃度”，Valkey 是三者里最稳的开源路线：Linux Foundation 托管、BSD 3-Clause、TSC 治理清晰。出处：Linux Foundation 发布稿、Valkey Leadership 页面。  
- 如果你最在意“官方功能推进速度和商业主导”，Redis 2025 明显最激进：2024 从 BSD 改成 RSALv2/SSPL，2025 又给 Redis 8 加回 AGPLv3 选项，同时整合 Redis Stack 能力进核心。出处：Redis 官方 licensing 页面与 AGPL 公告。  
- 如果你最在意“多线程 Redis fork 的历史路线”，KeyDB 仍有价值，但 2025 公开维护信号明显弱于 Redis/Valkey：官方文档确认它在 Snap 内部继续开发，但我找到的公开 release 最新仍是 v6.3.4（2023-10-30），没有看到像 Redis/Valkey 那样明确的 2025 对外版本节奏。出处：KeyDB docs About、GitHub Releases。
---
1) 2025 年三者的性能基准对比
先说明证据强弱
这部分我采用了 3 类原始来源：
1. 官方 Redis 基准：Redis 官方博客，方法公开，但不是三者同场对打。  
   来源：Redis 8.0-M02 官方博文  
   https://redis.io/blog/redis-8-0-m02-the-fastest-redis-ever
2. 官方 Valkey 基准：Valkey 官方 GitHub issue，给了实例规格、线程、pipeline、keyspace、时长。  
   来源：Valkey issue #1835  
   https://github.com/valkey-io/valkey/issues/1835
3. 独立可复现实测：第三方公开 benchmark repo，给出命令、环境、版本、结果表；不是官方，但不是“空口二手文”。  
   来源：centminmod benchmark repo  
   https://github.com/centminmod/redis-comparison-benchmarks/blob/master/readme.md  
   补充交叉验证：tidwall benchmark repo  
   https://github.com/tidwall/cache-benchmarks
2025 可引用的对比表
维度	Redis	Valkey	KeyDB	结论
官方/项目方公开性能信号	Redis 官方称 8.0-M02 对常用命令延迟显著下降，且基准可复现；使用 6 台 Ice Lake 服务器、40Gb 网络、redis-benchmark-specification 复现脚本公开。来源：Redis 官方博文	Valkey 官方在 c7g.metal x2、64 线程、650 clients、3M key、5 分钟 warmup + 1 小时统计下，8.1 RC1 相比 8.0 在部分场景提升明显，如 SET threads:9 pipe:4 +12%、GET 100% hit threads:9 pipe:4 +9%、ZADD single pipe:1 +10%。来源：Valkey issue #1835	KeyDB 没有找到 2025 官方发布的同等级 benchmark 报告；可公开引用的主要是独立复现实测。	2025 官方性能透明度：Valkey/Redis 强于 KeyDB。
同场独立实测（4 vCPU / 16GB / memtier / host network / 4 I/O 线程）	4 线程非 TLS：125,524 ops/s, avg 3.18ms, p99 7.65ms	4 线程非 TLS：98,119 ops/s, avg 4.15ms, p99 10.05ms	4 线程非 TLS：114,455 ops/s, avg 3.51ms, p99 10.37ms	在这组公开可复现实测里：Redis 吞吐/延迟最佳，KeyDB 次之，Valkey 第三。来源：centminmod repo
单线程表现（同一独立实测）	61,112 ops/s	56,000 ops/s	66,443 ops/s	同一测试里 KeyDB 单线程最好。来源：centminmod repo
1→4 线程扩展（同一独立实测）	+105%	+75%	+72%	这组数据里 Redis 扩展性也比 KeyDB/Valkey 更好。来源：centminmod repo
TLS 开销（同一独立实测）	吞吐 -33.4%	吞吐 -30.0%	吞吐 -30.3%	三者 TLS 开销都不小；这组数据里 KeyDB/Valkey 略好于 Redis。来源：centminmod repo
怎么解读这些基准
1. 没有一份“官方三方同场 benchmark”。  
   Redis 和 Valkey 各自都有官方/项目方数据，但不是同一套硬件同一脚本直接打三方，所以不能拿它们的绝对数值横向硬比。这也是我没有直接写“Valkey 一定比 Redis 快 X%”的原因。  
   来源：Redis 官方博文；Valkey issue #1835。
2. 真正能直接横向比较的，是公开方法的独立复现实测。  
   在我找到的可审查原始数据里，最适合三方横比的是 centminmod 的公开 repo：它给了版本、命令、线程、keyspace、TLS/非 TLS、延迟分位。  
   来源：https://github.com/centminmod/redis-comparison-benchmarks/blob/master/readme.md
3. 从“2025 生产选型”角度，性能不是单一答案：  
   - 追求最低延迟/最高吞吐：这轮公开三方同场数据偏向 Redis。  
   - 追求单机单线程峰值：这轮数据偏向 KeyDB。  
   - 追求开源治理稳定 + 持续性能演进：Valkey 更强，但在这份三方同场数据里绝对性能没有赢 Redis。  
---
2) 各自的维护状态和背后组织
项目	2025 背后组织	维护状态判断	依据
Redis	Redis Ltd. 主导	活跃且强商业驱动	Redis 2025 发布 Redis 8 GA，并更新官方 licensing/产品叙事，说明主线持续推进。来源：Redis 8 GA/AGPL 公告、Licenses 页面 https://redis.io/blog/agplv3/ (https://redis.io/blog/agplv3/) / https://redis.io/legal/licenses/ (https://redis.io/legal/licenses/)
Valkey	Linux Foundation / LF Projects，TSC 治理	活跃且治理最透明	Linux Foundation 在 2024 宣布成立 Valkey；Valkey 官方 Leadership 页面列出 TSC 成员和机构归属。来源：https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community (https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community) 、https://valkey.io/leadership/ (https://valkey.io/leadership/)
KeyDB	Snap Inc. 内部团队托管	仍在维护，但公开活跃度信号弱于前两者	KeyDB 官方文档写明“2021 年末 KeyDB 在 Snap 安家”，并称团队继续开发；但公开 release 页面最新可见到 v6.3.4（2023-10-30）。来源：https://docs.keydb.dev/docs/about (https://docs.keydb.dev/docs/about) 、https://github.com/Snapchat/KeyDB/releases (https://github.com/Snapchat/KeyDB/releases)
这部分最重要的判断
- Redis：不是“社区中立项目”，而是商业公司强主导。  
- Valkey：不是单厂 fork，而是基金会治理项目。  
- KeyDB：不是“没人管了”，但公开版本/治理透明度不如 Redis 和 Valkey。
---
3) 有没有重大许可证、所有权或战略变化
项目	重大变化	时间	为什么重要	出处
Redis	从 BSD 改为 RSALv2 / SSPLv1 双许可；2025 起 Redis 8 再加 AGPLv3 第三许可	2024-03 / 2025-05	这是三者里最大的战略变化。它直接影响云服务商、嵌入式商业分发、合规审查，也催生了 Valkey 分叉。	Redis 官方公告与 Licenses 页面：https://redis.io/blog/redis-adopts-dual-source-available-licensing/ (https://redis.io/blog/redis-adopts-dual-source-available-licensing/) 、https://redis.io/blog/agplv3/ (https://redis.io/blog/agplv3/) 、https://redis.io/legal/licenses/ (https://redis.io/legal/licenses/)
Valkey	因 Redis 改许可而诞生，基于 Redis 7.2.4 继续，以 BSD 3-Clause 保持开放分发；由 Linux Foundation 托管	2024-03	Valkey 的存在本身就是对 Redis 许可变化的回应。它的核心卖点不是“绝对性能领先”，而是许可证与治理稳定性。	Linux Foundation 发布稿：https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community (https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community)
KeyDB	我没找到 2024–2025 期间像 Redis/Valkey 那样的重大许可重构；但官方文档确认 2021 年末并入 Snap，并强调“只保留一个社区版本、把原先商业能力开放出来”	历史变化发生于 2021 以后、6.3.0 阶段公开化	对生产用户的意义是：KeyDB 的战略重心更像“Snap 内部孵化的高性能 fork”，不是基金会治理，也不是正在经历许可证重构。	KeyDB About、6.3.0 release notes：https://docs.keydb.dev/docs/about (https://docs.keydb.dev/docs/about) 、https://github.com/Snapchat/KeyDB/releases/tag/v6.3.0 (https://github.com/Snapchat/KeyDB/releases/tag/v6.3.0)
---
## 我排除掉的来源
我**没有采用**这类文章作为结论依据：
- 各种 “Redis vs Valkey vs KeyDB” SEO 对比文
- 没有硬件/命令/版本/线程参数的博客结论
- 只贴图不放方法或 raw repo 的二手摘要
原因很简单：你要求“排除没有原始数据的二手文章”，而这类内容通常无法审计。
---
最终建议：2025 生产怎么选
- 偏保守、要低延迟、接受商业主导：选 Redis。  
  理由：公开基准和产品推进最强，2025 Redis 8 明显是主线。  
- 偏开源治理、要避免许可反复、还要持续演进：选 Valkey。  
  理由：基金会治理 + BSD + 2025 仍有明确性能演进和组织投入。  
- 已经深用 KeyDB 特性，尤其多线程/Active Replication 路线：可以继续评估 KeyDB，但我会要求先做一轮你自己环境里的压测。  
  理由：公开维护信号和对外发布节奏弱于前两者，不能只靠历史印象下注。
如果你愿意，我下一步可以把这些内容整理成一个更适合内部汇报的 Markdown 决策表，再加一列“适合什么场景/不适合什么场景”。
