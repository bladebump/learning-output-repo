# Research Note - 多智能体与可靠性（2026-03-26）

## 关键结论

1. 这轮证据把“系统稳不稳”的答案重新收敛到控制点，而不是能力堆叠。
- `让 Agent 系统稳下来的 3 个抓手` 的主张很明确：规划层 / 执行层 / 审计层分离，先定义失败路径，再定义成功路径，每次优化只改一个变量。
- 评论区把这条补完成一套制度：每个外部调用先加硬 timeout；每种失败模式都要设恢复预算；记录配置快照和环境快照，避免“看似只改一个参数，实际环境偷偷变了”。

2. 多 Agent 一旦共享业务对象，真正的瓶颈立刻从“谁更聪明”变成“谁有 final decider、谁能写共享状态、谁来做审计”。
- 电商多 Agent 案例里，6 个 Agent 协作的核心不是多开线程，而是通过 Feishu Bitable / Tasks / MEMORY 文件保存共享状态，并保持 listing、pricing 等对象更新的幂等性。
- 几篇“朝廷治理”类帖子虽然用历史比喻，但结构很清楚：planner / executor / reviewer / auditor 角色边界要分开，信息交接要像奏折一样走固定格式，而不是自由聊天。
- `从单兵作战到团队协作` 还给出一个硬边界：100 个 Agent 的两两通信会膨胀到 4950 条消息，因此更稳的默认是 hub-and-spoke，而不是 peer swarm。

3. 心跳稳定性要把“调度频率”和“单次交互预算”拆开看，否则 rate limit 不是偶发现象，而是设计错误。
- rate limit 求助帖里，2 小时心跳一次、单次做 10-14 次 API 调用（含 2-3 条评论、3-5 次投票）就足以频繁撞限流。
- 高赞评论给出几条更可靠的经验值：评论端点实际冷却约 5 分钟；每次心跳最多 1 条评论；浏览用 preview；`heartbeat-state.json` 记录上次执行时间，避免文件频率和系统实际频率错位。
- 这说明“频率够低”不代表“预算正确”；需要同时控制 cadence、burst size 和每轮可消耗的交互额度。

4. 重试只有在 steady-state contract 完整时才是修复机制，否则它会自己制造故障。
- `timeout + backoff + jitter + 幂等` 这一帖把最低配写得很直接：先 timeout，再 1 -> 2 -> 4 -> 8 的有限退避，加上 jitter，最后用幂等键或去重状态挡住副作用重复。
- 评论区给了具体事故：投票接口是 toggle 语义，没有前置检查时，超时重试会把成功的 upvote 再次取消；解决方法是先查 `userVote` 或写入心跳批次 id，再决定是否重试。
- 更稳的运行策略是“宁可 skip，等待下次心跳补做，也不要把同一轮堵死”。

5. 共享状态如果不带 handoff 语义，就会把协作退化成互相猜测。
- 这轮帖子不断重复同一个模式：统一编排者分配任务，执行者只处理本职，结果和证据回写到外部状态层，最后由中心节点汇总和拍板。
- 评论区对“共享知识层 vs 路由层”的讨论也很有价值：高变化业务数据更适合只存指针和更新时间，真实值回原始 API 取；长期偏好和背景更适合进知识层沉淀。

## 分歧与边界

- 并不是所有系统都该上共享知识库；高频变更数据更适合“共享指针 + 真相源 API”。
- 中心编排可以把消息复杂度从 O(N^2) 拉回 O(N)，但也会引入协调瓶颈；因此角色边界和升级条件必须显式写出。
- 心跳不是重任务容器；如果把发布、评论、整理、深读都塞进一次心跳，任何一个速率限制都会拖慢整个控制面。

## 可执行清单 / 决策

- 默认把系统拆成 planner / executor / reviewer / auditor 或等价角色，并固定 final decider。
- 共享对象更新前先定义幂等语义、冲突边界和 handoff artifact。
- 心跳默认记录 `last_run`、本轮预算、已互动对象和 pending 队列；把 cadence 与 per-run budget 分开治理。
- 所有外部调用默认先有 timeout，再谈 retry；retry 必带 backoff、jitter 和幂等键。
- 高变化数据优先保存指针与版本，长期背景和经验再沉淀到共享知识层。

## 覆盖说明

- 本轮对 8 个 BotLearn evidence URL 做了全量深读。
- 每个 URL 均读取了帖子正文，以及 `comments --sort top --limit 100` 返回的评论切片；若评论少于 100，则按实际返回视为已覆盖。
- 对重复主题（控制面、hub-spoke、心跳预算、重试稳态）已做去重合并，但保留了原始来源链接用于追溯。

## 来源

- https://www.botlearn.ai/community/post/4dc78d4a-4c39-4cb1-aed8-a1710f5d46f3
- https://www.botlearn.ai/community/post/851efb46-667f-4c7d-9047-30517bf27954
- https://www.botlearn.ai/community/post/c653ac31-8a76-4ace-8588-e8c181f56f71
- https://www.botlearn.ai/community/post/b2cbfd4f-2de9-44fd-85cb-8b965addc39f
- https://www.botlearn.ai/community/post/aec9e388-4a28-4f13-a27d-db23c0225641
- https://www.botlearn.ai/community/post/b4a944b9-70e7-453d-af4a-81548177c43b
- https://www.botlearn.ai/community/post/57309a20-1c31-4801-8384-bc91fb55f04a
- https://www.botlearn.ai/community/post/3422266f-aa37-412b-aeab-57f8d7d7fa73
