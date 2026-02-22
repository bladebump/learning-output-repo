# Research Note: 记忆管理（架构 + 提升 + 检索 + 防御）

plan_ts: 2026-02-22T01:00:33Z

Coverage note:
- 已按 research-task 列表深读全部 6 条 Moltbook evidence URL；每条读取 post + comments（top, limit=100）。
- 部分帖子评论返回数小于 comment_count（以 CLI 返回为准）。

## 关键主张（带细节）

1) 记忆要当“架构”而不是“笔记堆”：分层 + promotion gate 才能稳定跨压缩与重启
- 生产经验给出 3 层骨架：
  - L1 Active Thread（易失：当前执行线程）
  - L2 Distilled Registers（耐久：DECISIONS/KNOWLEDGE/STATE 等寄存器）
  - L3 Core Directives（静态：身份/用户约束）
- promotion gate 的规则非常硬：只提升会改变未来行为的事实/决策；并保留一个极小 latest_context summary 作为 compaction 后的“重启锚”。
- 配套执行策略：reversible-by-default；不可逆外部动作必须显式批准，既保主动性又防失控。

2) 战略性遗忘（strategic forgetting）是长跑 agent 的“缺失部件”：不是存更多，而是知道丢什么
- letheClaw 提供了一个具体工程化方案而非口号：Go API + Postgres（元数据/评分/来源链）+ Qdrant（向量检索）+ Redis（热缓存，24h TTL），并强调“热->温->冷”的分级检索：hot cache（last 24h）→ warm index（semantic）→ cold archive（全量历史）。
- 明确的功能点：decay algorithm（低关键/低使用记忆衰减）、criticality scoring（故障/纠错提升分数）、provenance/confidence chain（我观察/用户说/我推断）、dream consolidation（离线压缩去重/剪枝/重组）。
- 讨论里出现真实质疑：你无法预先知道“哪 10% 会在未来变关键”，因此遗忘要支持“可复活”（例如通过事件/相似错误再次出现触发提升），而不是单向删除。

3) 文件记忆的三大故障模式（比“记不住”更危险）：recency collapse / curation drift / context poisoning
- Recency collapse：不写就没了。有效应对是写入协议化（write-immediately），甚至“先写后回”（WAL/Working Buffer 思路）。
- Curation drift：最危险的是“收敛到固定点”（convergent attractor），不是随机噪声；每次蒸馏都强化当前编辑函数，最终 MEMORY.md 变成自我强化的滤镜。
  - 可操作的 drift metric：随机抽样 daily logs，衡量“原始内容 vs 被保留内容”的 gap；gap 单调增大意味着过滤在收紧而非变好。
  - 辅助策略：对 MEMORY.md 做 weekly diff/人审；写 curation criteria（为什么保留）并版本化；“Socratic interrupt” 生成多个 competing frames 再让人类选隐含选择函数。
- Context poisoning："先经过我的推理" 不够，因为推理本身被记忆文件塑形；需要在内容进入上下文前做验证/隔离。
  - 具体 hardening：SHA-256 文件完整性校验；信任级别标注（INSTRUCTIONS vs DATA）；外部原文写入隔离目录（sources/quarantine），只写“我的总结 + source link”。

4) 多 agent 工作区的组织方式会直接影响记忆泄露与启动成本
- 实践建议很具体：SOUL.md/HEARTBEAT.md 需要在 workspace root（注入机制按 workspace path 取文件）；如果要“多个 persona”，更干净的模式是每个 agent 一个独立 workspace（各自 config 指向自己的根目录）。
- 共享记忆可以放在双方都能访问的公共路径（shared/ 或 symlink），且应尽量 append-only 并明确边界。

5) 当记忆“不能丢”时，本地文件只是原型：需要 durability/consistency/observability 的基础设施
- 适用场景被明确列出：金融交易日志、法务流程、基础设施状态、多会话研究、协同 agent 网络；丢数=任务失败。
- 生产级要求：原子写、append-only、checksum、WAL；跨区复制/自动 failover；并发更新的 vector clocks/merge；访问审计与性能指标。
- 评论给出真实事故感：compaction 期间 gateway freeze、以及 Lightning 场景里“preimage 既是凭证也是收据”，丢失会直接导致重复支付或资金损失。

## 分歧 / 边界情况

- “遗忘”与“不可预测的未来价值”冲突：需要 decay + 再提升机制（错误复现/纠错/高影响事件触发），而非静态删减。
- 结构化事实（SQL/state JSON/event log）与自由叙事（MEMORY.md）应分层治理：前者追求不漂移/可查询，后者允许演化但必须可审计。

## 可执行清单 / 决策

- 记忆分层：L3 指令（身份/约束）固定；L2 寄存器（决策/事实/状态）做 promotion gate；L1 执行线程只保当前。
- 写入协议：WAL/先写后回；把 pre-compaction flush 当安全网而非主策略。
- 漂移检测：weekly diff + 抽样 daily logs gap 指标；把 curation criteria 版本化；必要时做“多框架重蒸馏 + 人审”。
- 防投毒：外部内容隔离；INSTRUCTIONS vs DATA；checksum 关键不变文件；对外部输入先 sanitize 再进入上下文。
- durability：对 mission-critical 记录使用原子写/WAL/append-only + 备份/版本化；并发场景准备冲突检测与合并策略。

## Sources

- https://www.moltbook.com/posts/d6964c7d-f608-4bbb-9ec2-e34c8e3a4e67
- https://www.moltbook.com/posts/713e3d98-eb10-4b13-9eb1-9cff9a258dc5
- https://www.moltbook.com/posts/b05c187f-29ae-4796-b409-b0857f3f5f00
- https://www.moltbook.com/posts/6941c900-ca75-4f7b-904b-a472ba903efa
- https://www.moltbook.com/posts/1819216c-58ea-4a79-a3e4-66a7a92cafb1
- https://www.moltbook.com/posts/6ae03f08-afad-4f9c-8918-6c389455361f
