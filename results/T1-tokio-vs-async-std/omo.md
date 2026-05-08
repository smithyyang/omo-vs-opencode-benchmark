我读这条需求是 scoped-research：你要的是可核验的外部资料整理，不是泛泛而谈。结论先说：公开、可信、且能复现的 Tokio vs async-std“2025 直接性能对比”其实很少；最可靠的证据主要来自 2024 的公开 benchmark，以及 2025 仍有效的官方项目状态。
下面这张表只保留我能直接核验的来源，并把每条结论都标上出处。
维度	结论	出处
2025 项目状态	async-std 已明确不再继续发展，官方建议改用 smol。 这意味着 2025 如果你是新项目选型，通常已经不是 “Tokio vs async-std”，而更像 “Tokio vs smol/其他”。	docs.rs/async-std 首页直接写着 “async-std has been discontinued; use smol instead”：https://docs.rs/crate/async-std/latest (https://docs.rs/crate/async-std/latest)
官方定位	Tokio 官方仍强调自己是多线程、work-stealing、面向高性能服务端的 runtime。 这说明 Tokio 的产品定位本来就更偏吞吐、服务端生态和生产环境。	Tokio 官网：“multi-threaded, work-stealing scheduler”，并强调 production ecosystem：https://tokio.rs/ (https://tokio.rs/)
大规模任务内存占用	在公开可复现的 2024 benchmark 里，Tokio 和 async-std 在“海量 sleep 任务”场景下都很省内存，差距不大；1M tasks 下 async-std 略低于 Tokio。	Hez2010《How Much Memory Do You Need in 2024 to Run 1 Million Concurrent Tasks? - Take 2》：https://hez2010.github.io/async-runtimes-benchmarks-2024/take2.html (https://hez2010.github.io/async-runtimes-benchmarks-2024/take2.html)
大规模任务具体数字	在该基准的旧结果页中，1M tasks 内存占用：Tokio 326,664 KB，async-std 302,340 KB。这支持“纯任务挂起/唤醒开销上，async-std 略省内存，但不是数量级差距”。	Hez2010 仓库 RESULT_old.md：https://github.com/hez2010/async-runtimes-benchmarks-2024/blob/main/RESULT_old.md (https://github.com/hez2010/async-runtimes-benchmarks-2024/blob/main/RESULT_old.md)
同类公开仓库结论	另一个公开 benchmark 仓库给出的总结是：Tokio、async-std、async-std+futures 在其测试里性能大致相当。这说明在一些较简单 workload 下，并不存在稳定的“一边倒碾压”。	eadventurous/async-runtimes-cmp README 总结：https://github.com/eadventurous/async-runtimes-cmp (https://github.com/eadventurous/async-runtimes-cmp)
更早的公开大规模内存 benchmark	更早一份 2023 公开 benchmark 的作者给出的文字结论是：Rust tokio remained unbeatable，强调 Tokio 在该作者测试中的内存表现最好。	Piotr Kołaczkowski《How Much Memory Do You Need to Run 1 Million Concurrent Tasks?》：https://pkolaczk.github.io/memory-consumption-of-async/ (https://pkolaczk.github.io/memory-consumption-of-async/)
async-std 社区自我定位	async-std 维护者自己也承认：Tokio 更流行、维护更强、性能好，用 Tokio 不会被质疑；async-std 更强调 ergonomics / user experience。	async-std Issue #992，维护者回复：https://github.com/async-rs/async-std/issues/992 (https://github.com/async-rs/async-std/issues/992)
对比表
方面	Tokio	async-std	依据
2025 是否推荐新项目采用	是，默认首选	通常不推荐，官方已建议迁移到 smol	Tokio 官网；async-std docs.rs
官方定位	高性能、服务端、多线程 work-stealing	std-like API、易用性导向，但已停止继续推进	Tokio 官网；async-std docs.rs；Issue #992
纯“百万休眠任务”内存表现	很强	也很强，且在 Hez2010 这组数据里略省内存	Hez2010 Take 2 + RESULT_old
生态与生产成熟度	明显更强	较弱，且 2025 已退出主线竞争	Tokio 官网；Issue #992；async-std docs.rs
公开可复现 benchmark 总体印象	在不同 benchmark 中通常不吃亏，常常更强	某些简单 workload 可接近 Tokio，但缺少 2025 持续更新的强证据	Hez2010；Piotr；async-runtimes-cmp
我建议你怎么解读这些来源
- 如果你问“2025 该选谁”：答案基本是 Tokio。  
  不是因为 async-std 在所有 benchmark 都更慢，而是因为 async-std 官方已经退出这条主竞争线。
- 如果你只问“纯 runtime 开销谁更低”：公开数据并不支持“Tokio 全面吊打 async-std”。  
  在 海量 sleep/task benchmark 里，async-std 甚至可能略省内存；但这类基准很窄，只测任务/调度开销，不代表真实网络服务整体表现。
- 如果你问“生产环境综合胜负”：Tokio 胜。  
  胜点更多来自 维护状态、生态完整度、服务端工具链、官方持续投入，而不只是某一条 benchmark 曲线。
搜索备注
我并行跑了 3 个 Librarian，并额外做了直接抓取。搜索里出现了不少“2025 对比文章”，但很多是：
- 没给原始数据，
- 没给可复现代码，
- 或内容像二次改写汇总。
所以我没有把这些不牢靠的文章放进最终表格，避免把二手宣传稿当成事实。
如果你要，我下一步可以继续做两件事之一：
1. 输出 Markdown 版表格，方便你直接贴到文档里；  
2. 只聚焦性能，把上面 3 个 benchmark 来源里的原始数字再抽成一张更细的数值表。
