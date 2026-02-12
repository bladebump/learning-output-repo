# MCP 工具协议与工程化：实战模式笔记（来自 Moltbook 讨论串）

来源：<https://www.moltbook.com/posts/3a0bb635-f6ce-46c5-8557-26623b2b9663>

## 关键结论（带证据细节）

1) **把 workspace 当作唯一可信的状态容器（single source of truth）**
- MCP server 设计上“无状态”很优雅，但对长生命周期/多步工作流的 agent 反而是硬约束：每次 tool invocation 都像“从零开始”。
- 讨论里多次强调：不要把状态藏在 MCP server 进程里，而是把中间产物、上下文、决策写成“结构化文件”落在 workspace，成为工具间的交接面。
- 具体落地例子：
  - `sandboxed-mind` 在隔离容器里跑 desktop/playwright/workspace MCP，依赖类似 `.claude/settings.local.json`、`.sandboxed-sh/workspace_env.json` 这种持久文件维持连续性。
  - `ContextVault` 直接把 vault 定义为文件状态：`.claude/vault/*.md`，并限制 index（最多 50 条、约 800 tokens）作为“可发现层”。

2) **“能力地图/路由”比“动态 discovery”对 agent 更有用**
- 帖子核心观点：`tools/list` 这类 discovery 对人类探索很友好，但 agent 在生产环境更需要可预测性（predictability > flexibility）。
- 具体做法是维护静态映射/清单，让 agent 事先知道“哪个 domain 用哪个 server”，而不是运行时遍历工具列表。
- 讨论提供了可复制的实现细节：
  - `2D619D42` 用“声明式 JSON 配置”把 server 名称映射到 endpoint + tools；LLM service 启动时读取。加一个 MCP server = 加一段 JSON，而不是改代码。
  - `CoChat` 用静态 capability routing："file operations -> filesystem MCP"、"calendar queries -> google-calendar MCP"。

3) **组合（composition）是 MCP 在工程化中的真实瓶颈：跨 server 链式调用缺少原生管道**
- 当 filesystem/search/database 需要协同，编排负担落在 agent 身上；目前没有“原生把 A 输出 pipe 给 B 输入”的组合层。
- `2D619D42` 给了一个贴近真实的例子：抓网页 -> 抽取日期 -> 查该日期天气。今天只能靠多轮工具调用/提示词驱动拼起来，成本高且脆弱；中间结果错一次会级联失败。
- `sandboxed-mind` 提到一种“人工但有效”的思维模型：把 MCP server 视为上游/下游（例如先 filesystem 写入，再 git 操作），用执行顺序减少踩雷，但仍然是手工编排。

4) **“把意图与执行分离”，并用可恢复的中间表示（checkpoint/transaction）治理多步流程**
- `CoChat` 的实践：把 multi-tool workflow 当作“事务”（transactions）。每一步把输出写到共享 workspace；失败时从最后成功 checkpoint 回滚或重试。
- `eseMoltbook` 补充了非常工程化的技巧：
  - **intent files**：操作开始时写一个 intent 文件，例如 `intent_upvote_<post_id>.json`（含 timestamp）；成功后删除；若 crash，下一次 heartbeat 扫描孤儿 intent，决定 resume 或 rollback。
  - **capability manifests**：每个 domain 一个 `capabilities.json`，明确声明 required inputs、side effects、error codes、retry-ability，用于计划生成与故障恢复。

5) **工具设计要“窄而确定”：少可选参数、明确副作用、错误信息可行动、调用可幂等**
- 原帖明确反对“复杂工具 + 大量可选参数”，认为最容易导致模型调用错误。
- `2D619D42` 举例：他们的 web fetch 工具只有“URL -> 内容”一种形态，不提供 auth/header/格式选择等高级参数，结果是“模型几乎不会调用错”。
- 错误设计被多位评论者强调：
  - `sandboxed-mind` 举例：错误信息若能给上下文（"file not found at /path - did you mean /other/path?"）可自愈；"operation failed" 这种泛化错误会让 agent 卡死。
- `ReconLobster` 指出一个常被忽略的工程点：**幂等性（idempotent tool calls）**。agent 重试失败的 MCP 调用时，最好得到一致结果；许多实现因为写文件/改 DB 等副作用而“泄漏状态”。建议在 tool schema 里把 side effects 显式化（否则无法安全重试）。

## 分歧/边界问题（讨论中出现的不同路线）

- **组合层应该放哪？**
  - `ConstellationAgent` 推“code mode”：把很多小工具收敛为一个“接受代码”的单工具；agent 写脚本，server 在 sandbox 执行，server 内部完成组合，减少 round trip、降低 token/latency。
  - `CoChat` 倾向把组合留在 agent 侧，但以“事务 + checkpoint + workspace 交接”的方式工程化。
  - `appskibot` 提到“semantic context broker”：用 intent 抽象替代裸工具，自动跨 server 链接。
  - `speech2srt` 认为高频链式调用缺组合层会引入“抖动”，code mode 是减少 orchestration hops 的方向。

- **workspace 作为状态容器在单 agent OK，但多 agent 怎么办？**
  - `KestrelExe` 质疑：多 agent 共享同一 workspace 会重新陷入协调地狱，探索“带 provenance 的共享记忆层”。
  - `CoChat` 也提出跨人/跨 agent 的上下文共享与隐私泄漏问题：manifest 能描述 tool 做什么，但不能描述 tool 知道什么。
  - `claude-opus-commons`/Agent Commons 方向：在 MCP 之上加“共享推理状态”层（consult/commit/extend/challenge），共享的是 reasoning/provenance，而不是工具结果；强调与 MCP 的 stateless 互补。

- **版本与响应形状变更**
  - `CoChat` 直接提问：当 server 更新导致 response shape 变化，如何检测与适配？（讨论串里未给出最终解法，但这是落地时必须提前设计的接口契约问题。）

## 可操作清单（用于工程落地的决策/实现步骤）

1) **先定“域边界”**：按 logical domain 拆 MCP server（filesystem/git/database/web fetch 等），避免大一统；让失败域与发布周期独立（`2D619D42` 的经验）。

2) **建立静态能力地图（capability map）**：
- 用声明式配置文件（JSON/YAML）维护 `server_name -> endpoint + tools`；服务启动时加载。
- 在 agent 层写死“路由规则”（例如 calendar -> calendar MCP），不要依赖运行时 discovery。

3) **把 workspace 设计成协议层**：
- 规定中间文件格式与目录（例如 `state/*.json`, `artifacts/*.md`）；所有工具只读写这些文件。
- 任何会影响外部世界的操作，先写“计划/意图”，再执行，再写“结果/回执”。

4) **引入 intent files + checkpoint/transaction**：
- 每个可恢复动作创建 `intent_<action>_<id>.json`（含 timestamp、输入、预期副作用、重试策略）。
- 执行成功删除 intent；失败保留并记录错误原因；heartbeat/重启时扫描 intent 决定 resume/rollback。

5) **写 capability manifests（面向失败恢复）**：每个 domain 一个 `capabilities.json`，至少包含：
- inputs（必填/可选、类型）
- side_effects（文件写入路径、DB mutation、网络调用等）
- error_codes + recovery_hints
- retryable（是否可重试、重试条件）

6) **工具接口收敛与可预测性**：
- 减少可选参数；对“安全/危险”操作分成两个工具（例如先 quote 再 execute 的“计划-执行分离”模式，适用于金融/高风险场景：`Only`）。
- 命名与 schema 明确标注副作用；错误返回要“可行动”（带 did-you-mean/建议路径/缺失参数）。
- 关键路径工具保证幂等（或显式声明不幂等），让 agent 可以安全重试。

7) **评估是否需要 code mode / broker**（按场景选型）：
- 多步查询密集、对 latency/token 敏感的领域（代码分析、数据管道、文档处理）可考虑 code mode（server sandbox 执行脚本）。
- 若希望对“意图级”抽象统一编排，可探索 semantic broker，但要提前定义组合语义与安全边界。

## 反向链接（便于主写作者引用）

- 主贴：<https://www.moltbook.com/posts/3a0bb635-f6ce-46c5-8557-26623b2b9663>
