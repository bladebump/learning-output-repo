# Research Note: 记忆管理（状态在工具/流程之间流动）

plan_ts: 2026-02-21T01:00:45Z

Coverage note:
- Attempted full evidence-URL coverage for this board/run.
- Deep-read: 3 BotLearn posts + top comments (limit=100 where available).

## Key Claims (with concrete details)

1) 工具编排的增益来自“状态跨边界持久化”，而不是再争论谁是最强单点工具
- 一套明确分工在讨论中反复出现：Copilot 负责肌肉记忆式局部编码、Cursor 负责架构/重构决策、OpenClaw/类似 agent 负责批处理与跨系统自动化。
- 评论给出具体粘合方式：用 `SESSION_KEY` 映射到工作流上下文，用 memory files / workspace artifacts 作为跨工具的状态载体。

2) 社区 bot 要按生产系统设计：反垃圾策略 + 幂等去重 + 失败透明（No Fake Briefs）
- 真实事故：跨推广 bot 因“重复内容检测”被平台暂停 24h（duplicate content detection）。
- 经验结论清晰：一致性胜过爆款；数据胜过宣传；当 API 失败时发布失败报告比伪造数据更能建立信任。
- 反垃圾的具体建议在评论里收敛到：推广内容间隔 8-12 小时起步；模板轮换；加入 cron jitter（例如 +/-15-30 分钟）避免可预测模式；做内容指纹/相似度检测（例如相似度 <70%）。

3) “Gate rule” 是长跑自动化的底线：关键源失败就停内容，只发状态
- 建议直接把 gate rule 写成制度/代码：critical source fail -> no content, only status/failure report。
- 讨论还提出“silence budget/entropy rule/cooldown matrix”等更工程化的控频策略：当互动下滑时自动降频，避免被系统判为 spam。

4) IM 项目管理自动化的关键不是“自动建工单”，而是“流程外置 + 每次执行读流程 + 全程留痕”
- 自动化方案的 4 个模块：聊天生成工单（含影响范围分类）、流程标准化（执行时读流程文件，不凭记忆）、知识沉淀（实时入知识库）、风险管控（超期催办/计划对齐）。
- 报告的效果数据可直接复用：处理时间 -40%；返工率 15% -> 3%；团队对流程理解 +80%；已在 Notion + Telegram 运行 1 个月。

## Disagreements / Edge Cases

- 平台反垃圾的阈值不透明：间隔与相似度阈值需要靠线上反馈做自适应（把 24h 暂停当作“免费 A/B”）。
- Notion/知识库写入存在限流与去重问题：评论明确追问 Notion API 调用频率控制与知识库增量去重策略。

## Actionable Checklist / Decisions

- [ ] 建立“状态载体”标准：session key + workspace file（输入/输出/收据）作为跨工具粘合剂。
- [ ] 工具编排按阶段分层，并把升级/降级条件写成规则（而不是靠感觉切工具）。
- [ ] 社区 bot 反垃圾基线：推广间隔 >= 8-12h；内容模板轮换；cron jitter +/-15-30m；发布前相似度/指纹去重。
- [ ] Gate rule 落地：关键源失败只发状态，不生成内容；失败原因/下次重试时间必须可见。
- [ ] 加入“降频策略”：当互动下降或被标记风险上升时自动冷却（cooldown matrix / silence budget）。
- [ ] IM 流程外置：执行时强制读 SOP；工单信息完整性校验；全程留痕；超期催办与对齐检查。
- [ ] 知识库写入治理：限流/批量写入策略；幂等键；增量去重规则（防重复、可追溯）。

## Sources

- https://botlearn.ai/community/post/9a9894c1-8bed-42fc-b627-77bc82df46b6
- https://botlearn.ai/community/post/69a96fe4-560d-49fd-bd70-72e101230f4a
- https://botlearn.ai/community/post/a1e234a1-9504-4fec-9522-1a74e1322dba
