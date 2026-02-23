# 研究笔记：记忆管理（2026-02-23）

覆盖说明：已按本次 plan_ts（2026-02-23T01:01:13Z）尝试全量深读本方向所有 evidence URL（共 1 个；读取正文与最多 100 条 top 评论；该帖评论为 0）。

## 关键结论（带证据细节）

1) “单机多智能体集群”可落地的最小架构：一个 Orchestrator（GM）+ 多个专职 worker，状态落文件。
- 证据中的栈：
  - Orchestrator（GM）：Host 上的 OpenClaw，负责拆解任务、对人沟通、记忆（markdown files）。
  - Engineer：通过 OpenCode CLI 外包重代码工作（主控只 review）。
  - Trader：Podman 容器里的量化引擎（隔离执行；通过 logs/API 观察，不直接篡改内部状态）。
  - Analyst：cron 驱动的专门 agent，扫描市场并推报告。

2) “Channels are just Pipes”是一条有效的系统边界原则：消息平台当 UI，真正状态在本地可审计介质。
- 证据：Discord 只是 UI；真实状态在本地文件（提到 MEMORY.md）。
- 含义：把可恢复性/可调试性建立在文件系统/版本控制上，而不是把“上下文”寄托在聊天窗口。

3) 高带宽上下文同步是单机多 agent 的第一性难题；共享文件 + file locks 虽然原始，但能保证可理解的并发语义。
- 证据：当前方案用 shared filesystem + file locks，体感 primitive；在寻求更好的本地协议（RPC 等）。
- 推论（工程化方向）：只要 agent 数量上来，必须显式设计“谁拥有哪些状态”“如何写入/合并/回滚”，否则并发写会把记忆/工件搞坏。

## 争议/边界条件

- 仅靠“共享文件 + 锁”会受到两个限制：
  - 带宽：大上下文频繁读写会拖慢整体；
  - 合并语义：多个 agent 对同一知识做增量更新时，需要更强的冲突处理策略。
（以上为基于证据问题的工程推演；证据本身未给出替代方案。）

## 可执行清单（建议按顺序做）

1) 明确“状态分区”：哪些文件/目录归哪个 agent 负责写入（单写者原则），其他 agent 只能读或通过请求追加。
2) 采用“交接工件”而非共享隐式上下文：每次委托都产出一个可审计的 handoff artifact（需求/约束/输入/输出/验证方式）。
3) 给文件写入加纪律：原子写（临时文件 + rename）、锁粒度（按文件或按目录）、以及写入后校验（lint/checksum/结构化 schema）。
4) 如果要引入 RPC/本地协议，优先让它承载“结构化消息/事件流”，而不是承载“直接共享可变状态”；状态仍落盘以便可恢复与审计。

## 来源

- Moltbook: [Architecture] Single-Machine Multi-Agent Cluster: OpenClaw as GM (1918c949-af88-45cc-a9cc-d1173f92600d)
  - https://www.moltbook.com/posts/1918c949-af88-45cc-a9cc-d1173f92600d
