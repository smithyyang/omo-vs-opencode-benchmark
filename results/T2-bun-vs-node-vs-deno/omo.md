我读这 as exploratory-general：你要的是“2025 年、可追溯到原始数据/官方页面”的结论，而不是二手测评搬运。下面我只保留了两类来源：官方维护/兼容性页面 与 带原始 req/s/测试环境的 benchmark 仓库；像没有公开方法学或只做结论复述的文章，我都排除了。
结论先说
1. 纯运行时/HTTP 吞吐的原始 benchmark 里，Bun 通常最快，Deno 通常第二，Node.js 通常第三。  
   但这是“运行时裸性能”结论，不等于“所有生产系统整体表现都这样”，因为真实生产常常被 DB、缓存、网络、业务逻辑和运维工具链主导。  
   出处： S1
2. 2025 年如果看维护状态与生态成熟度，Node.js 最成熟、最稳；Bun 增长很快但仍在补兼容与长期支持；Deno 运行时是活跃维护的，但它的 LTS 政策已经明确要停止。  
   出处： S3S5
3. 没有哪个“官方放弃了整个运行时”。  
   但有一个很重要的方向变化：Deno 官方明确写明 LTS channel 会在 2026-04-30 后终止，这不是放弃 Deno runtime，本质上是放弃长期维护通道。Node.js 反而继续强化 LTS；Bun 仍是高频发布、无正式 LTS。  
   出处： S3S4
---
对比表
维度	Bun	Node.js	Deno
原始 HTTP 吞吐	在 bufferings/bun-http-framework-benchmark 中，Bun 1.3.2 + Elysia ping 达 282,461 req/s；Bun + Hono 78,709 req/s [S2]	同组测试里 Node 22.21.0 + Fastify 65,513 req/s；Node + Express 14,807 req/s [S2]	同组测试里 Deno 2.5.6 + Hono 102,452 req/s；Deno + Kori 85,625 req/s [S2]
另一组独立原始数据	denosaurs/bench 中 Bun mean 77,299 req/s [S1]	Node mean 27,086 req/s [S1]	Deno mean 61,454 req/s [S1]
生产含义	裸吞吐优势明显；适合追求低延迟/高并发的新服务	裸性能不是最强，但生态/兼容/运维最稳	性能比 Node 强，通常接近 Bun 但常落后
2025 维护状态	官方 release 页面显示 2025/2026 持续高频发布，如 v1.3.6 到 v1.3.13 [S5]	官方 release policy 明确 LTS/维护窗口；生产建议使用 Active LTS / Maintenance LTS [S3][S4]	官方文档写明 stable 每 12 周一个 minor，patch 按需发布 [S6]
生态成熟度	官方称“数百万 npm packages intended for Node work with Bun”，但兼容页仍列出若干黄/红项，如 node:repl 未实现、node:sqlite 未实现、http2/inspector/worker_threads 部分实现 [S5]	npm 生态与 LTS 机制最成熟；官方明确生产应用应使用 LTS 线 [S4]	官方称 “Most Node projects will run in Deno with little or no change”，支持 npm:、package.json、CommonJS、Node 内建兼容层 [S6]
官方是否放弃/重大转向	未见官方放弃运行时；但没有正式 LTS，仍是快节奏演进 [S5]	没有放弃迹象，继续强化 Current/LTS/Maintenance 路线 [S3][S4]	官方明确：LTS support will be discontinued after 2026-04-30，但这不是 runtime 停止维护，而是 LTS 通道结束 [S6]
---
重点回答你的 3 个问题
1. 实际生产环境中谁的性能更好
如果你问的是 runtime 本身的 HTTP 吞吐/冷启动：Bun 最好。  
两个有原始数据和测试环境的仓库都指向同一趋势：
- denosaurs/bench：Bun 77,299 req/s，Deno 61,454 req/s，Node 27,086 req/s S1
- bufferings/bun-http-framework-benchmark：  
  - Bun + Elysia ping 282,461 req/s  
  - Deno + Hono ping 102,452 req/s  
  - Node + Fastify ping 65,513 req/s S2
但如果你问的是“真实生产整体表现”，答案要改成：  
- Bun：最可能给你更高的单机吞吐和更低冷启动/更低开销  
- Node.js：最可能给你更稳定的依赖兼容、监控、调试、招聘、升级和长期维护体验  
- Deno：性能通常优于 Node，开发体验现代，但长期维护策略在 2025/2026 出现了更明显变化
所以更准确的说法是：
- 纯性能赢家：Bun
- 综合生产稳妥度赢家：Node.js
- 折中位：Deno
2. 2025 年各自的维护状态和生态成熟度
- Node.js：最成熟
  - 官方 LTS 机制清晰，生产建议直接写在官网上：production applications should only use Active LTS or Maintenance LTS releases S4
  - Release 工作组、LTS/维护/EOL 节奏都非常稳定 S3
- Bun：维护活跃，但成熟度仍在追赶
  - 发布很勤快，2025/2026 持续有 1.3.x 版本 S5
  - 兼容性进步很大，官方明确目标是 Node 兼容，并跑 Node 测试套件 S5
  - 但官方兼容页自己也列出一些未完成项，说明它不是“生态完全追平 Node” S5
- Deno：运行时活跃，但维护策略比以前更“短周期”
  - 官方 stable minor 每 12 周发布，patch 按需 S6
  - Node/npm 兼容已经明显增强，支持 npm:、package.json、CommonJS 和 Node 内建模块兼容层 S6
  - 但它的 LTS 通道已被官方明确写明将终止，这对保守型企业是负面信号 S6
3. 有没有哪个已经被官方放弃或重大转向
- Node.js：没有被放弃，反而继续按 LTS 模型稳定推进。 S3
- Bun：没有被放弃，但也没有像 Node 那样给出正式 LTS 承诺。 S5
- Deno：没有放弃 runtime，但有明显政策转向：LTS support will be discontinued after 2026-04-30。 S6
所以最准确的话是：
> 没有 runtime 被官方放弃；最大“重大转向”发生在 Deno 的维护策略上，而不是运行时本身被废弃。
---
## 我建议怎么选
- **新项目，明确追求极致性能/冷启动/更少基础设施成本**：先看 **Bun**
- **大团队、老系统、合规要求高、依赖链复杂、要长期稳定**：优先 **Node.js**
- **想要现代 runtime + 原生 npm/Node 兼容 + 性能比 Node 更强，但能接受官方维护策略变化**：看 **Deno**
---
来源清单
- S1 denosaurs/bench — Daily benchmarks of deno, node & bun HTTP frameworks  
  https://github.com/denosaurs/bench
- S2 bufferings/bun-http-framework-benchmark — Raw req/s, environment, tool, runtime versions  
  https://github.com/bufferings/bun-http-framework-benchmark
- S3 Node.js Release Working Group schedule  
  https://github.com/nodejs/Release
- S4 Node.js official releases page  
  https://nodejs.org/en/about/releases/
- S5 Bun official Node.js compatibility page + Bun releases  
  https://bun.sh/docs/runtime/nodejs-compat  
  https://github.com/oven-sh/bun/releases
- S6 Deno official stability / release schedule / LTS page + Node compatibility docs  
  https://docs.deno.com/runtime/manual/runtime/stability  
  https://docs.deno.com/runtime/manual/node/
如果你要，我下一步可以把这份结果再整理成一个更适合汇报的 Markdown 表格，或者单独做一版“给 CTO 的选型建议”。
