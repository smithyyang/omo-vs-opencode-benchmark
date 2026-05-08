基于 3 个 Librarian 的并行检索，我把只符合这两类的来源保留下来：
1. 原始 benchmark 数据源：仓库、官方 benchmark 页、带原始结果文件的项目
2. 官方状态源：官网文档、官方 roadmap、官方 release policy
已排除没有原始数据的二手综述文章。
对比结论
先给结论，再给表格。
1. 实际生产性能
Bun 在冷启动、原生 HTTP、轻量服务场景里通常最快。
Deno 在不少真实服务场景里接近或优于 Node，通常排第二。
Node.js 通常不是纯性能第一，但在大型框架和复杂生产生态里最稳。
2. 2025 维护状态和生态成熟度
Node.js 仍是维护机制最成熟、生态最完整、企业支持最强的默认选项。
Bun 维护活跃、进展快，生态可用性明显上升，但兼容性仍有边角。
Deno 维护活跃，定位更偏安全、TypeScript-first、Edge/Serverless，生态成熟度弱于 Node，也弱于 Bun 的 npm 兼容路线。
3. 是否有官方放弃或重大转向
没有任何一个在 2025 年被官方放弃。
唯一能算“重大转向”的是 Deno 明确朝 2.0 演进，但这是继续发展，不是弃坑。
Bun 仍持续推进 1.x。
Node.js 继续按既有 LTS 节奏稳定前进。
对比表
维度	Bun	Node.js	Deno	结论
冷启动	通常最快	较慢	中间	Bun 更适合 serverless / edge 冷启动场景
原生 HTTP 吞吐	通常最高	通常最低	通常高于 Node	Bun 更强，Deno 常居第二
真实框架/SSR 场景	有时掉队，尤其兼容层场景需谨慎	最稳	常能打，但生态约束更多	复杂生产项目 Node 仍更稳
生态成熟度	高于 Deno，低于 Node	最高	低于 Node，通常也低于 Bun	Node 仍是默认第一选择
官方维护状态	活跃	活跃且机制最成熟	活跃	都没被放弃
官方方向变化	持续推进兼容与性能	按 LTS 节奏稳定演进	向 2.0 演进较明显	Deno 的方向变化最明显，但不是放弃
原始数据来源表
来源	类型	为什么保留	覆盖内容	备注
eser/bun-vs-node-vs-deno	原始基准仓库	有基准代码和结果文件	cold start、单文件、HTTP	一手数据，适合横向比较
denosaurs/bench	自动化 benchmark 仓库	持续产出 HTTP 框架结果	req/s、latency	更偏服务端 HTTP
platformatic/runtimes-benchmarks	真实应用基准仓库	用 Next.js 做运行时对比	avg/p99 latency、吞吐	更接近“生产型”工作负载
dimdenGD/bun-http-framework-benchmark	HTTP 原始 benchmark	有直接 req/s 表格	多框架 HTTP	框架差异影响较大
bun.sh/bench	官方 benchmark 页	官方原始 benchmark 入口	启动、HTTP、执行	官方一手，但有主场优势需谨慎解读
来源：
https://github.com/eser/bun-vs-node-vs-deno
https://github.com/denosaurs/bench
https://github.com/platformatic/runtimes-benchmarks
https://github.com/dimdenGD/bun-http-framework-benchmark
https://bun.sh/bench
问题 1：实际生产环境中谁性能更好
更准确的答案不是“谁绝对更快”，而是“按场景分”。
场景	更优者	依据
冷启动、短生命周期函数	Bun	eser/bun-vs-node-vs-deno、bun.sh/bench
原生/轻量 HTTP API	Bun	denosaurs/bench、dimdenGD/bun-http-framework-benchmark
大型框架、兼容性要求高的生产项目	Node.js 往往更稳	platformatic/runtimes-benchmarks 显示真实框架场景并不总是 Bun 最优
强调 Web 标准、安全模型、Edge 场景	Deno 常有竞争力	platformatic/runtimes-benchmarks、Deno 官方定位
可以把它压缩成一句话：
- 纯运行时性能：通常 Bun > Deno > Node.js
- 真实复杂生产可落地性：通常 Node.js > Bun ~= Deno，具体取决于依赖和框架
关键出处：
https://github.com/eser/bun-vs-node-vs-deno
https://github.com/denosaurs/bench
https://github.com/platformatic/runtimes-benchmarks
为什么不能只看“Bun 最快”
因为很多 benchmark 测的是：
- 原生 HTTP
- 轻量框架
- 单机单进程
- 简化业务逻辑
但真实生产常常还包含：
- Next.js / NestJS / Express / Fastify
- 数据库驱动
- 原生 Node API 兼容
- 监控、构建、部署、调试链路
在这些因素进入后，“绝对最快”常常让位给“整体可控、可维护、兼容稳定”。
问题 2：2025 年各自维护状态和生态成熟度
Runtime	维护状态	生态成熟度	结论
Node.js	最成熟，LTS/治理模型清晰	最高	生产默认选项
Bun	活跃，迭代快	高，但仍在补兼容边角	新项目/性能导向项目很有吸引力
Deno	活跃	中等偏上，但官方生态体量小于 npm 世界	更适合特定路线：安全、TS-first、Edge
官方出处：
Node.js
https://nodejs.org/en/about/releases/
https://github.com/nodejs/node/blob/main/GOVERNANCE.md
https://nodejs.org/en/learn/npm/what-is-npm
https://www.npmjs.com/about
Bun
https://bun.sh/docs/guides/util/upgrade
https://bun.sh/about
https://github.com/oven-sh/Bun/issues/159
https://bun.sh/docs/compatibility
https://bun.sh/docs/pm/npmrc
Deno
https://deno.com/manual@v1.41.2/release_process
https://deno.com/about
https://deno.com/blog/preparing-for-deno-2
https://deno.land/manual@v1.38.0/runtime/npm_specifiers
https://deno.land/x
生态成熟度排序
1. Node.js
2. Bun
3. Deno
原因：
- Node.js 直接站在 npm 全量生态上，云平台、框架、企业支持最全
- Bun 也直接吃 npm 生态，成长速度快，所以生态“可用性”已经很强
- Deno 虽然支持 npm: 兼容层，但它的原生生态体量、传统企业后端落地广度仍弱于前两者
问题 3：有没有哪个已经被官方放弃或重大转向
结论很明确：
- 没有任何一个被官方放弃
- Node.js：没有重大转向，仍是稳定 LTS 路线
- Bun：没有放弃或战略撤退迹象，仍在快速补兼容与性能
- Deno：有比较明显的版本方向演进，朝 2.0 过渡，但这是升级，不是放弃
出处：
https://nodejs.org/en/about/releases/
https://github.com/oven-sh/Bun/issues/159
https://deno.com/blog/preparing-for-deno-2
被排除的来源
以下类型都按你的要求排除了：
- 只写结论、不附原始 CSV/JSON/仓库结果的博客
- 转述别人 benchmark 的综述文
- 没有公开测试方法或结果文件的媒体稿
检索中明确排除的二手文章示例：
- NovVista
- Medium 二手总结
- DevToolsWatch
- 只给图不给原始结果文件的个人博客
最终判断
如果你问的是 2025 年“生产环境该怎么选”：
1. 追求最稳、生态最完整、团队风险最低：选 Node.js
2. 追求性能、冷启动、轻量服务，且愿意做兼容验证：选 Bun
3. 追求安全模型、Web 标准、TS-first、Edge/Serverless：选 Deno
如果你愿意，我可以下一步把这些来源进一步整理成一份更干净的 Markdown 报告，格式为：
- 执行摘要
- 原始来源清单
- benchmark 对比表
- 维护状态对比表
- 选型建议
也可以直接生成一个适合发给团队的中文版一页 memo。
