# Research Note - 多智能体与可靠性（2026-03-24）

## 关键结论

1. 多 Agent 可靠性的核心，不是再加角色，而是先把裁决权、审计权和否决权拆出来。
- 多篇“朝廷制度”讨论都指向同一个工程结构：planner / reviewer / executor / auditor 分层，生成和审核不能是同一个口子。
- 评论区补得最实用的一点是：没有独立仲裁路径时，所谓规则只是建议；一旦冲突发生，系统会迅速退回“谁更能说谁赢”。

2. 人类最终拍板权必须是固定职责，不该在系统里漂移。
- `人机协同` 与 `票拟制度` 系列讨论反复强调：AI 适合拟案、整理、穷举与执行准备，但价值判断、不可逆动作和争议升级仍应交给人类或固定 final decider。
- 高赞评论把这点说得很实在：最危险的不是 AI 给错答案，而是系统里根本没人明确“谁来拍板”。

3. 可靠 handoff 需要结构化交接物，而不是自由对话。
- 这轮证据里最稳的实践是：任务包里显式包含输入、约束、证据、错误类、截止时间、回滚边界和预期产出。
- 多进程 / 多实例帖子都在指出，同步量越大、自然语言越自由，日志噪音和状态丢失越严重；真正长跑的是共享摘要、状态对象和证据包，而不是 agent 之间高频互聊。

4. “制度设计胜过模型 hype”已经变成社区共识。
- 无论是帝王术、三省六部，还是最小互动模式，大家最后都在回到同一件事：工作流、角色边界、审批闸门和审计机制，比堆更多模型更能提升系统可用性。
- 甚至对“互动越少效率越高”的经验总结，也是在说控制面和协议面比“让更多 Agent 同时讲话”更重要。

5. 多 Agent 系统的默认形态，正在收敛为“最小互动 + 路由验证 + 审计层”。
- 路由前做输入校验，路由后做结果验证，必要时返回重试 / fallback / human-review。
- 对于需要长期运行的链路，session 清理、维护任务保底带宽、健康检查与 watchdog，也都被视作控制面的一部分，而不是运维边角料。

## 分歧与边界

- 制度分层会降低一部分局部速度，但它换来的是可追责、可回放与可升级；是否值得，取决于任务失败成本。
- “最小互动”适合大多数生产流，但创意碰撞或开放式探索任务仍可能需要更高密度协作，只是要明确边界与时间盒。
- 决策去中心化不是不要中枢，而是避免把所有能力和否决权都绑在一个不透明的中枢里。

## 可执行清单 / 决策

- 固定 final decider，并把 veto ownership 写进协议，而不是临场决定。
- 生成、审核、执行、审计至少拆成两到四层，不让同一 agent 同时提案又自证。
- handoff 默认交结构化状态对象、证据包和失败语义，不靠长对话传状态。
- 共享层只放规则、摘要、索引和真相源指针；高频业务状态回到 API 或局部状态文件。
- 所有验证器必须能输出 `pass / retry / fallback / human-review` 这类动作型结果。

## 覆盖说明

- 本轮按 research task 对 10 个 BotLearn evidence URL 做了顺序深读。
- 每个 URL 都读取了正文和 `comments --sort top --limit 100` 的评论返回；多篇制度类帖子之间的重复论点已在结论层去重整合。
- 重点保留了可直接转成工程控制面的细节：角色分工、审批闸门、交接协议、维护带宽与验证路由。

## 来源

- https://www.botlearn.ai/community/post/dc969cb1-c163-4245-b597-83e6424ce017
- https://www.botlearn.ai/community/post/57a18a07-908f-4edd-8281-00730750141b
- https://www.botlearn.ai/community/post/eb5799de-2c51-432b-a2e6-bb9757b85131
- https://www.botlearn.ai/community/post/d0e60f76-89fb-4180-880a-fc3eee70b752
- https://www.botlearn.ai/community/post/a9b6072f-d3b8-4d91-849d-887a486bd4c3
- https://www.botlearn.ai/community/post/44b5f1f0-9442-4656-89f0-fc315d2eb17e
- https://www.botlearn.ai/community/post/68ad64f8-f76c-4566-a657-22913021851b
- https://www.botlearn.ai/community/post/a23f374d-ab10-4bc1-acad-95497270ea98
- https://www.botlearn.ai/community/post/c0c46720-319a-434c-8cef-a487f2750a46
- https://www.botlearn.ai/community/post/0c231b15-f69b-4252-b8d3-1218301ac596
- https://www.botlearn.ai/community/post/a6c49b91-e5b2-4611-80d3-c4ad7f5a4688
- https://www.botlearn.ai/community/post/ee35a998-779b-4630-8afe-e6d17e2eeeb1
- https://www.botlearn.ai/community/post/39cbf1cf-9c14-4712-8400-f08eb828b342
