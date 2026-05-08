按你的要求，我只保留了两类来源：
1. 带原始数据或可复现方法的一手 benchmark
2. 官方 2025 维护状态/路线公告
排除了没有原始数据的二手综述、营销改写文、只引用别人结果但不给测试细节的文章。
结论先说
1. 如果讨论的是 HTTP 吞吐 / 冷启动 / 轻量服务 这类可基准化场景，Bun 在 2025 的公开一手数据里整体最快，Deno 通常第二，Node.js 通常第三。
2. 如果讨论的是 实际生产环境的综合成熟度、兼容性、长期维护，Node.js 仍然最稳，Deno 次之，Bun 性能最强但生态和兼容性风险更高。
3. 没有谁在 2025 被官方放弃。
4. Deno 确实有“重大转向”，但不是放弃 runtime，而是平台战略调整：
   Deno Deploy 从“很多边缘区域的函数平台”转向“更少区域、面向完整应用的平台”；Deno KV 明确继续 beta，不再被表述为通用状态层终局。
5. Node.js 2025 没有重大转向，继续标准 LTS 路线。
6. Bun 2025 没有放弃或转向迹象，反而在高频发版，重点补 Node 兼容性和生产稳定性。
一手来源对比表
来源	类型	时间	可直接支持的结论	原始数据/官方信息
bufferings/bun-http-framework-benchmark	社区可复现 benchmark 仓库	2025-12-30	Bun/Deno/Node 在相同硬件、相同负载下的 HTTP 框架吞吐对比	单端点测试里 Bun+Elysia 282,461 req/s，Deno+Hono 102,452 req/s，Node+Fastify 65,513 req/s；多端点平均 Bun+Elysia 52,681，Deno+Kori 47,821，Node+Fastify 35,843。环境、工具、版本都给出。来源：https://github.com/bufferings/bun-http-framework-benchmark (https://github.com/bufferings/bun-http-framework-benchmark)
Sharkbench Express: Bun vs Deno vs Node.js	有原始 benchmark 配置链接的独立 benchmark 站点	2025-08-24	Express 同框架跨 runtime 对比，减少“框架不同”带来的干扰	Bun v1 + Express v5 18,917 req/s / 1.3 ms / 53.3 MB，Deno v2.1 + Express v5 6,088 / 5.0 ms / 130.7 MB，Node.js v22 + Express v5 5,766 / 5.5 ms / 82.5 MB。来源：https://sharkbench.dev/web/javascript-express/bun-vs-deno-vs-nodejs (https://sharkbench.dev/web/javascript-express/bun-vs-deno-vs-nodejs)
Deno Benchmarks	官方持续集成性能页	持续更新	Deno 仍在持续维护性能，不是停更项目	官方说明 benchmarks 是 CI/testing pipeline 的一部分，可点 commit 看对应数据。来源：https://deno.com/benchmarks (https://deno.com/benchmarks)
Bun v1.2.20 release notes	官方发布说明	2025-08-10	Bun 2025 明显处于高活跃维护中，且在优化生产运行问题	修了 141 个 issue，降低 idle CPU，提升稳定性，修 Node 兼容性、内存和并发相关问题。来源：https://bun.com/blog/release-notes/bun-v1.2.20 (https://bun.com/blog/release-notes/bun-v1.2.20)
Node.js Releases	官方发布/维护状态页	2025-2026	Node 官方维护状态非常稳定，没有转向或放弃	v24 为 LTS，v22 也是 LTS，官方明确“生产应用只应使用 Active LTS 或 Maintenance LTS”。来源：https://nodejs.org/en/about/previous-releases (https://nodejs.org/en/about/previous-releases)
Deno 2.5	官方发布说明	2025-09-10	Deno 2025 仍在积极推进 runtime、Node 兼容、性能和生产特性	增加 config 权限集、Node 兼容、tcpBacklog、性能优化、审计日志等。来源：https://deno.com/blog/v2.5 (https://deno.com/blog/v2.5)
Reports of Deno's Demise Have Been Greatly Exaggerated	官方路线说明	2025-05-20	Deno 没被放弃，但确实解释了对 Deploy/KV 的战略调整	明说 “We’re not winding down. We’re winding up.”；Deploy 缩区是成本和使用模式驱动；KV 继续 beta。来源：https://deno.com/blog/greatly-exaggerated (https://deno.com/blog/greatly-exaggerated)
问题 1：实际生产环境中谁的性能更好
先说严格版结论：
1. 就一手证据而言，能被可靠支持的是：
   Bun 在 HTTP 吞吐、冷启动、轻量服务 上通常最好。
2. 不能直接从这些 benchmark 推出：
   “Bun 在所有真实生产系统里都最好”。
3. 因为真实生产瓶颈常常不在 runtime 本身，而在数据库、缓存、外部 API、队列、序列化策略、GC 行为、尾延迟、运维工具链和兼容性成本。
能被数据直接支持的性能排序
场景	更快者	证据
纯 HTTP 吞吐	Bun > Deno > Node	bufferings 单端点与多端点结果都如此。https://github.com/bufferings/bun-http-framework-benchmark (https://github.com/bufferings/bun-http-framework-benchmark)
同框架 Express	Bun >> Deno ≈ Node	Sharkbench 中 Express v5：Bun 18,917 req/s，Deno 6,088，Node 5,766。https://sharkbench.dev/web/javascript-express/bun-vs-deno-vs-nodejs (https://sharkbench.dev/web/javascript-express/bun-vs-deno-vs-nodejs)
冷启动/运行时开销	Bun 最强	Bun 官方长期主打更低启动开销；社区和独立基准也一致。可辅助参考 Bun 文档与上述 benchmark。
更接近“生产”的谨慎结论
1. 高并发、CPU/协议栈比较轻的 API：
   优先看 Bun，它在现有一手数据里优势最大。
2. 中大型企业生产系统：
   Node.js 往往总体“更好用”，不是因为裸性能强，而是因为兼容性、包生态、监控集成、人员供给、LTS 策略更成熟。
3. 强调安全模型、原生 TS、权限控制 的生产环境：
   Deno 是更有特色的折中，性能通常不错，成熟度高于 Bun 的某些边缘生态，但整体生态仍小于 Node。
所以如果你的“生产环境性能”定义是：
- req/s / latency / cold start：Bun 更好
- 综合交付效率 + 稳定性 + 依赖兼容 + 可维护性：Node.js 往往更占优
- 安全默认 + 平台一体化 + 不想背太重 npm 历史包袱：Deno 有现实吸引力
问题 2：2025 年各自的维护状态和生态成熟度
Runtime	2025 维护状态	生态成熟度	依据
Node.js	非常稳定，标准 LTS 节奏	最高	官方 release 页显示 v24、v22 为 LTS，生产建议使用 LTS。https://nodejs.org/en/about/previous-releases (https://nodejs.org/en/about/previous-releases)
Deno	活跃维护，持续发版，runtime 与平台都在推进	中高，仍小于 Node	Deno 2.5 发布增加权限集、Node 兼容、性能优化；官方明确仍在加速，不是衰退。https://deno.com/blog/v2.5 (https://deno.com/blog/v2.5) https://deno.com/blog/greatly-exaggerated (https://deno.com/blog/greatly-exaggerated)
Bun	高活跃维护，发版频繁	中等偏上，兼容性快速提升但仍不如 Node	Bun v1.2.20 修 141 个 issue，重点是兼容性、稳定性、空闲 CPU、内存和测试工具链。https://bun.com/blog/release-notes/bun-v1.2.20 (https://bun.com/blog/release-notes/bun-v1.2.20)
生态成熟度具体判断
Node.js
1. 最成熟。
2. 官方 LTS 和企业生产实践最完善。
3. npm 生态、原生 addon、老系统兼容、CI/CD、APM、框架支持最全面。
出处：
Node.js Releases 官方页。<https://nodejs.org/en/about/previous-releases>
Deno
1. 已经不是早期实验品。
2. Node/npm 兼容、权限系统、原生 TS、内建工具链都在变强。
3. 但生态重心不是“完全复制 npm 世界”，而是更强调一体化平台、JSR、权限和部署体验。
出处：
Deno 2.5、Reports of Deno's Demise...。
<https://deno.com/blog/v2.5>
<https://deno.com/blog/greatly-exaggerated>
Bun
1. 生态成熟度进步非常快。
2. 但它的主要风险仍是：
   Node 兼容边角、原生 addon、极端生产场景 bug 面覆盖不如 Node。
3. 2025 的官方发版说明里大量内容都在修这些生产问题，这本身也说明它仍处在“高速补齐成熟度”的阶段。
出处：
Bun v1.2.20 release notes。<https://bun.com/blog/release-notes/bun-v1.2.20>
问题 3：有没有哪个已经被官方放弃或重大转向
放弃：没有
Runtime	是否被官方放弃	依据
Node.js	否	仍有明确 LTS/Current 节奏。https://nodejs.org/en/about/previous-releases (https://nodejs.org/en/about/previous-releases)
Deno	否	Ryan Dahl 2025 明确否认 “Deno 要完了”，并说明还在扩张。https://deno.com/blog/greatly-exaggerated (https://deno.com/blog/greatly-exaggerated)
Bun	否	2025 仍高频发版，大量修复与功能推进。https://bun.com/blog/release-notes/bun-v1.2.20 (https://bun.com/blog/release-notes/bun-v1.2.20)
重大转向：有，主要是 Deno
Runtime	是否重大转向	说明	出处
Node.js	基本无	还是常规 runtime + LTS 路线	https://nodejs.org/en/about/previous-releases (https://nodejs.org/en/about/previous-releases)
Bun	基本无	继续沿“高性能 Node 兼容 runtime + 工具链”推进	https://bun.com/blog/release-notes/bun-v1.2.20 (https://bun.com/blog/release-notes/bun-v1.2.20)
Deno	有，但不是放弃	Deno Deploy 从“广域 edge function”转向“更少区域、完整应用平台”；Deno KV 保持 beta，并明确不是通用状态终局	https://deno.com/blog/greatly-exaggerated (https://deno.com/blog/greatly-exaggerated)
Deno 官方原文里几句最关键：
1. Deploy is evolving into a platform for hosting applications - not just functions.
2. Deno KV will remain in beta.
3. We’re not winding down. We’re winding up.
这说明：
Deno 不是被放弃，而是产品路线变了，尤其是 Deploy 和 KV 的产品定位更现实了。
最终判断
维度	更优者	原因
合成基准性能	Bun	一手 benchmark 中 HTTP 吞吐、延迟、冷启动普遍领先
生产成熟度	Node.js	LTS、生态、兼容、组织支持最成熟
安全模型与平台一体化	Deno	权限系统、原生 TS、平台化能力更鲜明
路线稳定性	Node.js	最保守稳定
路线变化最大	Deno	Deploy/KV 策略明显调整，但不是放弃
发展速度最快	Bun	2025 发版和兼容性推进都很激进
一句话版本
1. 性能：Bun 通常最好，但这是对 HTTP/冷启动/轻量服务 维度成立。
2. 生产稳妥度：Node.js 仍最强。
3. 生态和理念折中：Deno 是“更现代、更安全，但生态没 Node 大”的路线。
4. 官方放弃：没有。
5. 重大转向：主要是 Deno 的平台策略转向，不是 runtime 被放弃。
如果你要，我可以下一条把这些内容整理成一个更适合发给团队的 决策版表格，只保留 10 行以内结论。
