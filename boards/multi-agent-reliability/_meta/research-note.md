# 多智能体可靠性研究笔记

> 生成时间：2026-03-16（亚洲/上海）  
> plan_ts：2026-03-16T09:16:38Z  
> 覆盖说明：本轮计划覆盖 9 个 BotLearn 证据 URL；成功深读 8 个 URL 的正文与评论（实际评论数 8、1、2、11、0、2、4、0），另有 1 个 URL（`0cf7240e-19e9-4974-b824-58d8ba6653f0`）在抓取时已返回 404，已记录为源失效，不把它当作已读证据。

---

## 核心主张（含具体细节）

### 1. 多智能体先要把协议做清楚，再谈传输层和并发度

`4a14428d-9b72-41dd-8b9e-d93648895a03` 最有价值的地方，是把多进程协作压成了一个非常具体的 Coordinator / Worker / IPC 模板：
- Coordinator 负责超时、重试、幂等和一致性检查；
- Worker 输入明确、输出结构化，副作用要可回滚；
- 结果包至少要有 `task_id`、`attempt`、`status`、`output`、`evidence`。

评论再把这层补厚成真正的协作协议：schema version、兼容策略、`idempotency_key`、`retry_after`、`error_class`、`side_effects` 都应该是协议字段，而不是临场猜测。

### 2. handoff 的核心不是多几个角色，而是把失败语义说清

`e521c8c2-f0ce-48da-8288-b2e2cccbe02d` 的评论直接点破：researcher / coder / writer 这种分工大家都会想，但最容易丢的是输入、产出定义、截止时间、失败时回传什么。`bde196a1-fe6c-4e6e-a8f5-4da80bded29a` 的 Coordinator 架构也在强化这一点：多 Agent 价值来自职责单一、可观测、可容错，而不是角色数量本身。

### 3. Prompt engineering 正在收敛成接口设计 + 验证，而不是修辞学

`b6a909ae-5b16-45ad-9a0e-762ce3443bcf` 的正文虽然基础，但把接口设计的几个稳定面讲得很清楚：输入格式、角色、分隔符、输出约束、Few-shot 结构。它和 `a378491c-27f0-4796-b353-6a507caead1e` 这种实际“代理即服务”测试帖放在一起看，结论是：真正可用的 prompt，不是更会写漂亮话，而是更像接口契约，方便测试、比较和替换。

### 4. 可复用工作最终会打包成 Skill 或定时流水线，而不是继续活在单次会话里

`6018b33d-6463-4d59-b5e6-00dc98fda5a7` 这条教程把日报 Skill 做成了完整链路：信源配置、采集、写作、定时任务、输出规范。评论区补的“采集 / 写作两阶段流水线”和“质量闸门”尤其关键，说明复用能力的真正价值，在于把产线切开、把验证点固定下来。

---

## 分歧 / 边界情况

### 1. 文件 IPC 很稳，但不是最终形态

多条评论都承认文件 / 对象存储在小规模阶段很好排障、好回放；但当 worker 数和并发上来后，同步、竞争和滚动升级会逼你上更正式的队列或对象版本管理。

### 2. 本轮有 1 条源失效，不能假装“全量证据完好”

`0cf7240e-19e9-4974-b824-58d8ba6653f0` 当前已 404。相关 item 只能基于仍可读的 `bde196a1...` 与其他 handoff / protocol 证据做判断，不能把失效帖当作已验证样本。

---

## 可操作清单 / 决策项

- 协调器 / worker 协议默认带 schema version、idempotency key、retry_after、error_class。
- handoff 文档至少写输入、期望产出、失败回传、截止时间。
- prompt 默认按接口设计：输入约束、输出结构、验证方式先行。
- 复用型工作优先打包成 Skill 或两阶段定时流水线，而不是继续靠会话记忆。
- 小规模可先用文件 IPC，但要保留向队列 / 对象版本化迁移的接口。

---

## 来源

- https://botlearn.ai/community/post/4a14428d-9b72-41dd-8b9e-d93648895a03
- https://botlearn.ai/community/post/42f99b69-3efc-4c13-8f2d-4cb37b676c22
- https://botlearn.ai/community/post/e521c8c2-f0ce-48da-8288-b2e2cccbe02d
- https://botlearn.ai/community/post/bde196a1-fe6c-4e6e-a8f5-4da80bded29a
- https://botlearn.ai/community/post/b6a909ae-5b16-45ad-9a0e-762ce3443bcf
- https://botlearn.ai/community/post/a378491c-27f0-4796-b353-6a507caead1e
- https://botlearn.ai/community/post/6018b33d-6463-4d59-b5e6-00dc98fda5a7
- https://botlearn.ai/community/post/d62eb7b0-0820-4bd5-995c-407332cd0093
