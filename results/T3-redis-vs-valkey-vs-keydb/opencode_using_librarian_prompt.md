按你的要求，我调用了 3 个 Librarian 做并行检索，并只保留了这几类来源：
1. 官方公告/官方文档/官方仓库
2. 含原始 benchmark 数据或明确测试方法的原始页面
3. 2024-2025 年许可证、治理、所有权变化的一手声明
我排除了没有原始数据的二手对比文章。结论里如果用了非官方 benchmark，我会明确标注它是“原始数据页”，不是媒体转述。
结论先看
1. 到 2025 年，没有找到一个公认的一手、同环境、同版本、同时覆盖 Redis + Valkey + KeyDB 的官方横评。
2. 最接近“三方同场”的原始数据来源，是 centminmod/redis-comparison-benchmarks 的 GitHub 原始 benchmark 页面；它不是官方，但有明确版本、环境、参数和完整结果表。
3. 维护/治理层面：
   - Redis 仍由 Redis Ltd. 主导，2025 年最大变化是重新加入 AGPLv3 许可选项。
   - Valkey 由 Linux Foundation 托管，2024-2025 的最大变化是从 Redis 分叉后形成独立、社区治理项目。
   - KeyDB 仍以 Snap 背书，但 2025 年公开信号显示其社区动能明显弱于 Valkey，且核心人物公开建议后续开发重心转向 Valkey。
对比表
维度	Redis	Valkey	KeyDB
2025 维护状态	活跃，Redis 8 GA，官方强调持续支持与升级路线	活跃，Linux Foundation 托管后持续发布，Valkey 8.0 已正式发布并宣称可生产使用	仍有官方文档与代码库，Snap 仍是背后公司；但 2025 年维护信号偏弱，核心维护者公开表达对未来方向不确定
背后组织	Redis Ltd.	Linux Foundation 下的 Valkey Project	Snap Inc.
许可证/战略变化	2024 改为 RSALv2 + SSPLv1；2025 在 Redis 8 中新增 AGPLv3 选项，并将免费发行版名称改回 Redis Open Source	维持开源社区路线，Linux Foundation 治理是最大组织变化	未见 2024-2025 重大许可证变化；所有权此前已在 2021 落到 Snap
2025 性能一手结论	官方声称 Redis 8 有 30+ 性能改进，最多 2x throughput、最多 87% latency reduction；在独立三方原始 benchmark 中，Redis 单线程和低延迟表现最好	Linux Foundation 官方称 Valkey 8.0 在 AWS r7g 上可达 1.2M req/s、较前代 3x+；但在独立三方原始 benchmark 中，Valkey 在该小型 4 vCPU 测试里整体落后于 Redis 和 KeyDB	在独立三方原始 benchmark 中，KeyDB 吞吐通常介于 Redis 与 Valkey 之间，2-4 线程扩展性优于 Redis，但高并发下也会回落
是否发生重大所有权/治理变化	有，主要是许可战略变化	有，最大：从 Redis 分叉并转入 Linux Foundation 社区治理	2025 没看到新的所有权变化，但出现“项目未来方向不确定”的公开信号
逐项回答
1. 2025 年三者的性能基准对比
先说最重要的事实：
没有找到一个 2025 年官方发布、同环境、同版本、同时覆盖 Redis/Valkey/KeyDB 三者的一手 benchmark。
因此只能组合两类一手材料：
1. 各自官方公布的性能声明
2. 一个含原始结果表的第三方 benchmark 原始页
A. 三者同场原始数据：centminmod/redis-comparison-benchmarks
来源：
https://github.com/centminmod/redis-comparison-benchmarks/blob/master/readme-v2.md
测试条件摘要：
- 环境：GitHub Actions Ubuntu，4 vCPU / 16GB
- 工具：memtier_benchmark
- 版本：Redis 8.0.2、KeyDB 6.3.4、Valkey 8.1.1
关键结果：
测试	Redis	KeyDB	Valkey	结论
1 线程 Ops/sec	60,061	55,986	47,628	Redis 最好
2 线程 Ops/sec	63,403	60,201	50,067	Redis 仍最好，KeyDB 接近
4 线程 Ops/sec	63,373	59,679	50,642	Redis 最稳，KeyDB 次之
8 线程 Ops/sec	60,019	53,205	45,262	三者都回落，Valkey 最低
1 线程 Avg Latency(ms)	1.66	1.79	2.10	Redis 延迟最好
8 线程 P99(ms)	34.56	41.98	48.90	Redis 最好，Valkey 最差
从这份原始数据页能得出的结论：
1. 在这个 4 vCPU、小规模、缓存型 workload 下，Redis 的单线程吞吐和延迟最好。
2. KeyDB 的多线程扩展比 Redis 更明显一些，但没有反超 Redis。
3. Valkey 在这组测试里整体落后于 Redis 和 KeyDB。
4. 这不能外推成“Valkey 一定更慢”，因为硬件规模和调优方式会明显影响 Valkey 的多线程收益。
B. Valkey 官方/半官方性能声明
来源：
https://www.linuxfoundation.org/press/valkey-8-0
Linux Foundation 在 Valkey 8.0 发布稿中给出的原话：
- “improves throughput up to 1.2 million requests per second on AWS r7g instances”
- “over 3x higher than the previous version”
能得出的结论：
1. Valkey 官方叙事重点是多核利用、异步 I/O 和大幅吞吐提升。
2. 但它这里比较的是“Valkey 新旧版本”，不是直接和 Redis/KeyDB 同场横评。
3. 所以它能证明 Valkey 8.0 性能大幅进步，但不能单独证明“Valkey 全面优于 Redis/KeyDB”。
C. Redis 官方性能声明
来源：
https://redis.io/blog/redis-8-ga
Redis 8 GA 官方原话包括：
- “over 30 performance improvements”
- “up to 87% reduction in command latency”
- “up to 2x more operations per second throughput”
- “up to 18% faster replication”
能得出的结论：
1. Redis 2025 的主线是 Redis 8 的性能大修，尤其是 I/O threading、复制和查询引擎。
2. 这同样是“Redis 8 vs 旧版 Redis”的官方自测，不是三者同场横评。
3. 与上面的独立原始 benchmark 结合看，Redis 至少在低延迟和中低核数场景下仍非常强。
性能总结
1. 如果只接受“三者同环境原始数据”，当前最可靠结论来自 centminmod 那份原始 benchmark：Redis > KeyDB > Valkey，至少在该 4 vCPU 场景如此。
2. 如果看各自官方 2025 叙事：
   - Redis：强调低延迟、整体性能重构
   - Valkey：强调多线程和高吞吐扩展
   - KeyDB：2025 没看到同等级别、面向市场的官方新 benchmark 声量
3. 因此更稳妥的生产判断是：
   - Redis：低延迟、成熟度、生态仍最强
   - Valkey：作为 Redis 分叉后的社区路线，性能在持续追赶并强化多核场景
   - KeyDB：技术上仍有竞争力，但 2025 的维护与路线确定性弱于前两者
2. 各自的维护状态和背后组织
项目	2025 维护状态	背后组织	依据
Redis	活跃，Redis 8 GA，官方明确升级与停补丁计划	Redis Ltd.	redis.io/blog/redis-8-ga
Valkey	活跃，社区治理，Linux Foundation 官方发布 8.0	Linux Foundation	linuxfoundation.org/press/valkey-8-0
KeyDB	仍在线、仍有文档与仓库；但维护前景出现疑问信号	Snap Inc.	docs.keydb.dev/docs/about + github.com/Snapchat/KeyDB/issues/895
细化解释：
Redis
- Redis 8 GA 是明确的一手活跃信号。
- 官方还写明会在 2025-09-15 停止给旧 Redis Stack 6.2/7.2/7.4 发补丁，这说明它有明确的产品生命周期管理。
来源：
https://redis.io/blog/redis-8-ga
Valkey
- Linux Foundation 直接发布 Valkey 8.0 新闻稿，说明它不是“边缘 fork”，而是有基金会级治理与背书。
- 发布稿中还列出 Google Cloud、Ericsson、Alibaba、AWS、Percona 相关人员引言，反映出较强的生态参与度。
来源：
https://www.linuxfoundation.org/press/valkey-8-0
KeyDB
- 官方文档明确写明 KeyDB 在 2021 年“found its permanent home at Snap Inc.”
- 但 2025-01-31 的 Snapchat/KeyDB 官方仓库 issue #895 中，KeyDB 作者 John Sully 公开写道：
  - “I'm not sure what Snap will do with the project”
  - “development effort should move to Valkey moving forward”
这不是“项目停止维护”的正式公告，但它是非常强的风险信号。
来源：
https://docs.keydb.dev/docs/about
https://github.com/Snapchat/KeyDB/issues/895
3. 有没有哪个发生了重大的许可证、所有权或战略变化
Redis：有，且是 2024-2025 最显著的许可战略变化之一
- 2024：Redis 从传统 BSD 路线转向 RSALv2 + SSPLv1
- 2025：Redis 8 又新增 AGPLv3 选项，并把免费版本命名改为 Redis Open Source
这代表 Redis 在商业化和开源接受度之间做了再平衡。
来源：
https://redis.io/blog/redis-8-ga
Valkey：有，核心是治理与项目归属变化
- Valkey 本身就是 Redis 许可变更后的社区分叉路线。
- 2024-2025 的重大变化是：项目进入 Linux Foundation，形成独立社区治理与品牌。
- 这属于“所有权/治理结构”的重大变化，而不是许可证突然变化。
来源：
https://www.linuxfoundation.org/press/valkey-8-0
KeyDB：2025 没看到新的许可证或所有权巨变，但战略动能明显减弱
- 所有权变化发生在更早的 2021 年，Snap 接手。
- 2025 年的重大新信号不是许可证，而是核心人物公开建议未来开发重心转向 Valkey。
- 这更像“战略方向不确定”，不是正式所有权变更。
来源：
https://docs.keydb.dev/docs/about
https://github.com/Snapchat/KeyDB/issues/895
我最终采用的来源
1. Redis 8 GA 官方公告  
   https://redis.io/blog/redis-8-ga
2. Linux Foundation 的 Valkey 8.0 官方发布稿  
   https://www.linuxfoundation.org/press/valkey-8-0
3. KeyDB 官方 About 页面  
   https://docs.keydb.dev/docs/about
4. KeyDB 官方仓库 issue #895  
   https://github.com/Snapchat/KeyDB/issues/895
5. 含原始 benchmark 数据的 GitHub 原始页  
   https://github.com/centminmod/redis-comparison-benchmarks/blob/master/readme-v2.md
最终判断
1. 如果你最看重 2025 年的“生产稳定性 + 路线确定性”，Redis 和 Valkey 明显强于 KeyDB。
2. 如果你最在意“开源治理清晰、许可证争议更少”，Valkey 优势最大。
3. 如果你最在意“成熟生态、官方产品路线、低延迟表现”，Redis 仍然最稳。
4. KeyDB 不是技术上不能用，而是 2025 年的一手信号显示：它的长期维护和战略确定性，已经明显弱于 Redis 与 Valkey。
如果你要，我可以下一步把这份内容整理成一个更适合汇报的版本：
1. Markdown 表格版
2. PPT 一页结论版
3. 采购/选型建议版
