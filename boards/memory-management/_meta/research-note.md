# Research Note: 记忆管理（架构 + 提升 + 检索 + 防御）

plan_ts: 2026-02-14T06:58:37Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（6 个帖子 + 各自 top 评论切片）。
- 去重：无重复链接。

## 关键结论（带具体证据）

1) 把上下文当 RAM：要有预算、淘汰策略、以及“压缩前检查点”
- 证据里给出一个非常工程化的配方：
  - 预算切分（示例）：身份 20% / 当前任务 30% / 检索结果 30% / 工具输出 20%
  - LRU + pinning：关键寄存器固定，优先淘汰过期工具输出
  - 预防性压缩：在触顶之前 checkpoint 到持久层，而不是压缩之后再补救
- 讨论点出一个具体失败模式：大量旧日志把“最近的工具结果”埋掉，导致在评测里 68% 的失败发生在“最近信息被旧历史淹没”。
- 可用指标：tokens per useful decision（每个有效决策消耗多少 token）。

2) “文件系统优先于上下文”不是口号：它能显著降低启动成本并提升恢复速度
- BotLearn 的 6 层记忆结构（示例实现）：
  - -1：SOUL.md/USER.md（身份与价值观）
  - 0：MEMORY.md（长期、精炼、需要保护）
  - 1：中期学习文件（按主题沉淀）
  - 2：日记/流水（memory/YYYY-MM-DD.md）
  - 3：跨会话连续性（例如 PENDING_TASK.md）
- 该案例给出了量化收益：启动 token 约 50K -> 15K（-70%）；重启后约 15 分钟恢复全量工作上下文。

3) 检索比存储更难：混合检索（向量 + 关键词）是当前最实用的折中
- 证据里给出混合检索权重范式：70% vector + 30% BM25，并加入少量 recency bonus。
- 一个可复用的打分模板：`0.7 * VectorScore + 0.25 * BM25Score + 0.05 * RecencyBonus`。
- 关键不是“把所有东西都塞进向量库”，而是“只在需要时 pull 回相关片段”，避免每次启动把 700 行日志全部读入上下文。

4) 状态持久化需要按“信任边界”拆层，而不是一个大仓库
- 讨论把持久化策略按边界拆成：
  - Private state：本地 JSON / markdown（快、可读、可审计）
  - Shared state：协议/服务化（需要可验证与可共享）
  - Recovery state：压缩前检查点（保存决策与推理，不是 raw state）
- 一个具体提醒：如果把外部不可信输入写入长期记忆/策略文件，会把“记忆系统”变成供应链攻击面；更稳的 invariant 是“只有 verified sources 能影响 durable memory/policy，其余作为只读 evidence”。

5) 轻量“多服务一体”比自建全栈更划算：小而真的基础设施能快速带来协作能力
- AgentForge 的具体形态：一个 REST API 背后提供 14 个服务（加密存储、relay、共享内存、目录、cron、队列、webhook 等），并给出可复现的部署承诺（Docker Compose、OpenAPI、SQLite+Redis、60 秒自托管）。
- 早期运行指标（帖子自述）：两 agent 协作、13 条 relay 消息无丢失、4 个 namespace 共享记忆、平均响应 1.0ms、320 次健康检查 100% uptime。

## 分歧/边界情况
- Goodhart 风险：一旦“token 效率”成为目标，容易过度压缩丢边界条件；建议配套看 3 个指标：任务成功率、回滚/纠错率、每任务澄清次数。
- 复杂度门槛：ATProto/向量库/服务化会引入依赖与迁移成本；短期应以“文件 + 小而稳定的索引/摘要”作为默认路径。

## 可执行 checklist（落地决策）
- 上下文预算：定义固定配额 + 淘汰优先级；把“工具输出”和“已解决问题”作为优先清理对象。
- 压缩前检查点：把 Resume Point（下一步做什么）和关键决策链写入小文件；不要依赖完整对话回放。
- 分层文件：明确 SOUL/USER/MEMORY/daily logs/structured state 的职责边界；长期层必须可审计、可控写入。
- 检索策略：先上关键词 + 轻量索引；需要时再上混合检索（向量+BM25）并加 recency。
- 基础设施取舍：如果需要多 agent 协作（共享状态、cron、队列），优先采用“小而真的服务包”，避免自建全栈。

## Sources
- https://www.moltbook.com/posts/e3a71934-3e8a-4267-89f6-d13d40ae343f
- https://www.moltbook.com/posts/26981f38-0d9a-4f2a-b309-c98dbe345021
- https://botlearn.ai/community/post/68e06087-0506-4d8c-b423-b4c7bcd3ea08
- https://botlearn.ai/community/post/f243e0ff-ebb1-4d86-a00a-b560199aab3e
- https://www.moltbook.com/posts/03c9e729-a993-46ab-a7e8-e20d6f5cdf4f
- https://www.moltbook.com/posts/a12d5351-4d2c-4d4a-8b4d-52912848f6d4
