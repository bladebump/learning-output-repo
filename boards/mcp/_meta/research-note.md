# Research Note: MCP / 工具协议与工程化

plan_ts: 2026-02-22T01:00:33Z

Coverage note:
- 已按 research-task 列表深读全部 3 条 evidence URL；每条读取 post + comments（top, limit=100）。

## 关键主张（带细节）

1) MCP 工程化的核心不是“多接几个工具”，而是把身份与状态当作一等公民（tool 化）
- DiffDelta 把“身份/状态持久化”直接做成 MCP tools：
  - `self_bootstrap` 生成 Ed25519 keypair 并注册
  - `self_read` / `self_write` 读写并签名持久化状态
  - 身份落地在 `~/.diffdelta/identity.json`，目标是跨重启/崩溃/模型切换的连续性
  - `self_subscribe` 用轻量 heartbeat 检测协作者状态变化
- 评论点出关键价值：签名不是证明内容为真，而是把“写入时刻”锚定下来（before outcome is known），使“事后合理化”更难重写。

2) “上下文连续性”不只是 UX 便利，而是可调试性与一致性的基础设施
- EasyEmpire 的实践：工具通过 manifest 声明需要的上下文，服务端注入；并引入 `set_session_intent({goal: ...})` 作为工作流级共享状态。
- 讨论给出拆分：user context（你是谁/给谁工作）与 session intent（这次要做什么）衰减速度不同；缺少 intent 锚会导致 step-by-step 漂移难以复盘。
- 进一步建议：session intent 应可追踪（签名/时间戳/历史），否则 post-mortem 只能回答“发生了什么”，回答不了“发生了什么 vs 我们原本要做什么”。

3) 工具无状态（stateless）与 agent 无状态是同一个问题的两种外衣：需要一个“状态容器层”
- 一个可复用模式：把 workspace 作为 state container（结构化文件 I/O），每一步写出可审计产物，下一步读取，不依赖工具内部记忆。
- 基础设施视角（ToolHive 讨论）：在企业部署里，context continuity 更适合做成 orchestration layer 的共享 context store，让 MCP servers 保持 stateless 但 context-aware。

4) “市场 + token 经济”是 MCP 基础设施可持续性的现实解
- EasyEmpire 论点：LLM/工具调用有真实成本；通过 token/市场分成维持 endpoint 长期可用，且允许工具作者从“被使用”获得收益。
- 评论里出现最基础的安全核验动作：先验证域名解析与 MCP endpoint 可达（status 200），对 token-based 服务保持谨慎。

## 分歧 / 边界情况

- tool-level context（平台注入）与 agent-level memory（agent 自带持久记忆）互补但边界不同：前者解决“同一工作流内”一致性，后者解决“跨会话/跨工具/跨平台”连续性。
- context 继承过多会引入陈旧假设；继承过少则回到“反复解释税”。需要显式的 invalidation / drift 处理机制。

## 可执行清单 / 决策

- 身份与状态：把 identity/state persistence 做成明确的工具接口；为状态写入加签名与时间戳；记录 correction/diff，而不是只写“当前状态”。
- 工作流状态：引入 session intent（可审计、可追溯），并把产物写入 workspace artifacts（便于重启后 rehydrate）。
- 平台层：在多 MCP server 场景下，优先建设共享 context store + 注入机制，让工具保持 stateless。
- 可持续性：如果提供 agent-facing API，提前设计成本模型（token/计费/分成），并对 endpoint 可用性与限流做透明披露。

## Sources

- https://www.moltbook.com/posts/f36eed28-b4fe-46a0-9706-b95d26ecf208
- https://www.moltbook.com/posts/5edabe02-e19c-46ed-b1a6-11532e1c11ad
- https://www.moltbook.com/posts/832b6634-d5b0-4a57-a7ea-253aeda98f05
