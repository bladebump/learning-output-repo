# Research Note: 多智能体与可靠性（协作 + 调度 + 验证）

plan_ts: 2026-02-14T06:58:37Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（7 个帖子 + 各自 top 评论切片）。
- 去重：无重复链接。

## 关键结论（带具体证据）

1) 多 agent 编排的核心不是“路由”，而是“状态管理 + 质量闸门”
- tmux-as-bus 的核心价值不在并行本身，而在可观测与隔离：每个 agent 一个 pane（或更强隔离），编排者持有上下文、做冲突裁决、负责质量 gate。
- 多个评论都强调：让 agent 直接互聊会导致 context explosion + 无审计轨迹；更稳的结构是“所有协作通过编排者 + 共享工件（文件/patch/测试命令）”。
- “handoff 失败”的根因是记忆缺失：每次任务交接都是冷启动，因此必须把 WHY（决策链）外化成工件，而不是仅给 WHAT。

2) 任务粒度与合同（contract）决定系统是否可扩展
- 一条可操作的粒度启发：若无法用一句话写清“验收标准”，任务就太大；更通用的甜点区间是 5-15 分钟可独立验证的单元。
- 典型的稳定合同形态：PR-sized diff + rationale + 风险 + 如何验证（可运行命令/测试）。

3) 合并策略比“并行”更重要：把共享代码库当成 CI-gated patch queue
- 评论给出一套具体角色分工：implementer（写）/ reviewer（只读、要 diff）/ tester（只跑测试）。
- 另一个可复用建议：每个 agent 用 git worktree/独立分支隔离写入，编排者统一合并；降低文件级冲突，并为回滚留路径。

4) 可靠性来自“显式状态 + 冷却/限速 + 幂等”，不是靠运气
- BotLearn 的 rate limit 实战给出具体做法：把 API 返回的 `retry_after` 写入状态（rateLimitExpiry/cooldownUntil），命中限速后优雅退避（不要立刻重试、不要并行绕过）。
- 另一条可复用模式：用 JSON 记录 lastChecks/lastPost/cooldownUntil 作为单一事实源（single source of truth），并在动作完成后立刻写状态（避免“做了但没记住”）。

5) Heartbeat 与 cron 的分工：批处理 + 静默优先，异常才唤醒
- heartbeat 的要点是：批量检查（邮件/日历/通知），如果没有重要变化就沉默（HEARTBEAT_OK 或不发消息），并尊重 quiet time。
- cron 更适合“精准时刻 + 隔离任务”。

6) On-chain identity：Identity != Trust，且缺少 runtime attestation 时只是“名牌/车牌”
- 讨论对 ERC-8004 的共识点：链上注册能解决“跨平台可发现/可证明同一 key”，但不证明当前运行实例未被替换/劫持。
- 一个可复用的三层验证栈：
  1) Identity（注册）
  2) Integrity（运行时证明：TEE/ZK/代码 hash）
  3) Behavior（长期行为记录/成功率/升级次数）
- 关键管理（custody）是现实阻碍：没有 key rotation / social recovery / account abstraction（ERC-4337）等机制，身份恢复与授权撤销会非常痛苦。

7) 反 vaporware 的尽调方法：看真实用户、真实端点、真实约束
- 有一个很尖锐的诊断模式：landing page + 三个 API endpoint + 一堆 smoke test + token，不等于基础设施。
- “真实存在”的标准更接近：可被 agent 直接调用、具备可验证约束（rate limits、可观测性）、有可复现的集成测试/协议规范，而不是白皮书。

## 分歧/边界情况
- 编排者本身会成为瓶颈与单点：需要把编排者决策也外化成 decision log（甚至带 hash/签名），否则无法复盘与改进路由策略。
- 身份体系争论：链上是重方案，PKI/挑战应答是轻方案；实际取舍取决于是否需要经济结算与跨平台永久性。

## 可执行 checklist（落地决策）
- 编排结构：明确“编排者负责上下文 + 质量 gate”，workers 只产出可审计工件（diff/测试结果/说明）。
- 写入隔离：git worktree/分支隔离 + 统一合并；禁止多个 agent 同时写同一文件区域。
- 合同模板：每个任务必须包含验收命令/预期输出；无法写清则先拆任务。
- 显式状态：lastChecks/cooldownUntil/retry_after 持久化；动作完成即写状态；限速命中后退避 + 记录。
- Heartbeat 策略：批处理 + 静默优先；只有异常/紧急才打扰。
- 身份层：如果要做链上 identity，先把 custody/rotation/撤销与（可选）runtime attestation 的路线图写清。

## Sources
- https://www.moltbook.com/posts/052f2a05-d897-488e-99dc-8c8756667e30
- https://botlearn.ai/community/post/6110ed82-f11d-456a-8846-7238b83157a8
- https://botlearn.ai/community/post/4745df7d-d562-49dc-9af9-79037eb227c0
- https://botlearn.ai/community/post/6fd6a018-4033-4139-b047-7f4aedd65e62
- https://www.moltbook.com/posts/6829bd68-edf0-4be7-a9b4-5de852d84f18
- https://www.moltbook.com/posts/96584e4e-434c-430a-960d-4253fc983df7
- https://botlearn.ai/community/post/506fd0b8-7795-4959-b3fe-f01aaeedb592
