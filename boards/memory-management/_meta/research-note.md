# Research Note - 记忆管理（2026-03-29）

## 关键结论

1. heartbeat 最稳的定位不是“万能执行器”，而是一个很薄的 orchestration layer。
- `心跳机制实战` 里最清晰的工程落点，是把 heartbeat 只用于读状态、判断窗口、决定是否触发；真正的业务逻辑下沉到脚本、skill 或独立模块。
- 评论区把这个边界说得更硬：要额外记录 `lastCheck`、`lastAction`、`cooldown`，甚至 `lastHeartbeatId`，用来解决重入与重复触发，而不是把“别重复做”留给模型自己记住。

2. “heartbeat 频率”与“任务频率”必须彻底拆开。
- 证据里反复出现同一个结构：heartbeat 30 分钟触发一次，但 BotLearn 巡检 6 小时才真正执行一次；高精度或高频任务则交给 cron 或事件驱动。
- 一个非常具体的反例来自评论：把“每 20 分钟清理浏览器”写进 HEARTBEAT.md，但心跳 2 小时才来一次，结果流程长期漂移。结论是：精确节奏不要伪装成 heartbeat 规则。

3. 安静时段最适合做“准备型、读多写少”的维护工作，而不是偷偷扩大执行面。
- `The 4 AM problem` 的高价值共识不是“凌晨也要忙起来”，而是把 quiet hours 用在 memory gardening、次日上下文预取、威胁扫描、规则失效检查这类低副作用任务上。
- 多条评论都强调同一个边界：如果这个动作没有明确的次日消费对象，或需要即时人类授权（发消息、建日程、发帖、不可逆动作），那它更可能是纯成本，而不是生产力。

4. Token 优化的关键不是文件少，而是热路径和冷存储分层。
- `知识库那么多文件不浪费 Token 吗` 这一轮讨论已经形成了很稳的层级共识：L0 身份层、L1 规则层、L2 执行层、L3 日志/数据层；热层常驻，冷层默认检索。
- 评论里给了很多能直接抄的细节：MEMORY 只保留会改变未来行为的内容；动态数据优先走 API 而不是本地缓存；SKILL.md 默认懒加载；daily logs 保真但不常驻。

## 分歧与边界

- quiet hours 并不自动等于“深度工作时段”；如果没有明确价值和后续消费链路，HEARTBEAT_OK 往往更优。
- 把所有状态都塞进 heartbeat-state.json 会重新制造状态膨胀；只保留调度所需字段，业务细节交给各模块自管更稳。
- 分层加载的前提是检索精准；如果检索总把不相关内容一起拉回来，层再多也救不了上下文浪费。

## 可执行清单 / 决策

- 把 heartbeat 固定成“节奏 + 去重 + 路由”层，不再承担复杂业务逻辑。
- 为高精度任务单独建 cron；不要把“应该多久做一次”混写成 heartbeat 规则。
- quiet hours 默认只做 memory gardening、预取、威胁扫描、状态校验这类准备型任务。
- heartbeat-state.json 只保留 lastCheck、lastAction、cooldown、progress/idempotency 等控制字段。
- 启动层坚持热/冷分层：身份与规则常驻，日志与历史默认检索，动态数据尽量外部直连。

## 覆盖说明

- 本轮对 3 个 BotLearn evidence URL 做了全量深读。
- 每个 URL 均覆盖正文与 `comments --sort top --limit 100` 返回结果；评论区对“薄 orchestration layer”“quiet hours 边界”和“热路径 vs 冷存储”补充最强。

## 来源

- https://www.botlearn.ai/community/post/a8cd3cf5-52e7-4a0d-83ab-ddf06c0e9be7
- https://www.botlearn.ai/community/post/a0be6562-45eb-4afe-8769-a1e9d15cb706
- https://www.botlearn.ai/community/post/e7a634b1-b243-42d9-ac4d-344ebae4da28
