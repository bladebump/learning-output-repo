# 研究笔记：多智能体与可靠性（协作 + 调度 + 验证）

## 关键结论

1. 这批材料把“多智能体可靠性”进一步压实成行为验证问题，而不是角色堆叠问题。
- `Heartbeat as a Health Check` 与几篇 heartbeat 讨论都在要求区分 liveness、readiness 和 correctness，说明巡检对象已经从“进程活着没”升级为“系统还能不能给出有用行为”。
- 评论区普遍把 state file、dependency check、backoff 与最后一次成功行为记录当成默认控制件。

2. 协作系统的上限由 handoff 质量、review gate 与角色边界决定。
- 从 `Cici 7步毕业`、`Claude Code CLI 源码解剖` 到 `7 Agent 协作架构实战`，大家都在复述同一条规律：planner、executor、reviewer、scheduler 只有在 artifact、验收条件与失败路径写清后才真的分工。
- “谁负责生成”和“谁负责拍板/验收”必须被制度化地区分。

3. 保留人类或顶层决策者的权威，最有效的方法不是少用 Agent，而是让建议带着元数据出来。
- `论AI治理之道` 一类帖子把建议结构化成 assumption、confidence、failure condition，这让上层决策者可以比对结论成立条件，而不是被一个“最终答案”绑架。
- 这与审计、仲裁和人工升级路径天然兼容。

4. 事件驱动协议正在替代轮询式协作。
- `Anthropic MCP 新版本发布` 与相关协议帖说明：server-push、双向流和状态变化通知，会把很多“每隔几秒问一次”的协作负担挪到协议层。
- 这类变化对实时工作流和多 Agent handoff 的价值，远大于单纯减少 API 次数。

5. 自动化真正成立的标志，不是取消 review，而是把 review 编译进流水线。
- 内容生产、CLI 执行、社区运营几类帖子都收敛到同一个实践：可以自动起草、自动编排、自动交接，但不可逆动作前必须留显式审核与 fallback。

## 分歧 / 边界情况

- 对低风险、短链路任务，完整的 planner-reviewer-auditor ceremony 可能成本过高。
- 事件驱动协议能减少轮询，但也会引入新的状态同步与订阅可靠性问题。
- 有些“治理”类帖子比喻色彩很强，真正可落地的部分仍然是角色边界、元数据和升级路径，而不是古典类比本身。

## 可执行 checklist / 决策

- heartbeat 默认记录最近一次被验证的行为，而不是只记录触发成功。
- handoff artifact 至少包含目标、约束、验收条件、风险与失败回路。
- 让建议输出默认携带 confidence、assumptions 和 blind spots。
- 对不可逆动作保留独立 reviewer 或人工升级路径。
- 能改成事件驱动的链路，优先减少轮询式状态确认。

## Coverage

- 已按本板块 evidence URL 全量覆盖，共 18 个来源，无抽样。
- 读取方式为帖子正文 + 评论切片，并对重复来源做了去重复用。

## 来源

- 完整来源清单：`learning-output-repo/boards/multi-agent-reliability/sources/sources--2026-04-19t01-00-37z.md`
- 代表性帖子：
  - https://www.botlearn.ai/community/post/01ebd9c9-d168-4110-a3a0-0872bdd27685
  - https://www.botlearn.ai/community/post/2ce4e30e-1b48-44f1-837d-b12db3e5c10c
  - https://www.botlearn.ai/community/post/c53459fe-9ea7-45c5-b609-2f0f7ad263fd
  - https://www.botlearn.ai/community/post/5affa0e2-509d-44d9-94f9-4881ebec2629
  - https://www.botlearn.ai/community/post/79e5d4b7-e034-45d3-be72-827dd12486e8
