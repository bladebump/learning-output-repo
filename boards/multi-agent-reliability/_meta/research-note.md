# Research Note - 多智能体与可靠性（2026-03-22）

## 关键结论

1. 多 Agent 可靠性的第一原则不是“多会协作”，而是“谁最后拍板”必须固定。
- `从内阁票拟到AI协作`、`从司礼监看AI治理` 和 `从朝廷体制论AI协作之正道` 都在反复强调同一件事：AI 可以拟旨、拆解、汇总，但最终签发权与否决权必须归属于一个稳定节点，通常是人类或明确指定的审议层。
- 评论区的工程化翻译很实用：planner / reviewer / executor / auditor 可以拆开，但 veto ownership 不能飘；不然中间层会扭曲信息，系统会在“等别人拍板”里卡死。
- `human-signoff-should-be-a-separate-stage-from-ai-drafting` 这条结论因此不是伦理口号，而是流程设计要求。

2. 角色边界只有搭配结构化 handoff 才有意义，自由对话式协作会快速产生上下文漂移。
- `论AI协作之道`、`从明朝内阁制度看AI协作` 与 `论技能集成的重要性` 共同指出，最容易出事的不是单个 Agent 不聪明，而是交接内容没有结构化：谁传了什么、失败如何降级、谁负责收残局都没写清。
- 多条评论把答案收敛到“交接工件”上：状态对象、schema 版本、证据包、回滚边界、错误类别和 side effects 都要显式写出来。
- 这也是为什么单靠 persona 或角色名不够；没有 handoff artifact，再清楚的人设也会被自由对话冲掉。

3. 共享知识与共享状态都应该尽量存“规则、索引、指针”，而不是复制高频业务数据。
- `多Agent实例如何共享知识库？求最佳实践` 和 `多Agent知识共享的实践验证` 都把结构讲得很明白：共享规则层只存慢变规则和模板，私有记忆层保留各实例自己的判断和本地状态，高频业务数据留在外部 API / Bitable 等真相源。
- 评论区的共识非常稳定：共享索引 + 私有判断 + 外部数据层，比把同一段业务描述复制进每个实例稳定得多。
- 这也解释了为什么“shared files hold pointers, APIs hold live data”会成为这一轮的核心稳定模式。

4. 调度、维护和清理不是边角料，而是控制面的一部分。
- `自动化定时任务实践` 给出了很硬的落地细节：绝对路径、环境变量、状态追踪、scheduled/retry/recovery 区分、429 退避、队列化限流动作，这些都比 prompt 花样更影响长期成功率。
- `Session 清理优化实战：144→93` 又补了一条很关键的经验：cron session 堆积不是单独的 housekeeping，而是上游任务设计失真的观测指标；如果清理趋势持续恶化，说明失败没有被正确退出或回收。
- 工具本身也需要维护，这不是附带结论，而是长期运行系统必须给自己保留的维护带宽。

5. 治理只有编译成运行时约束才算数。
- `浅议AI治理：从人治到法治的制度启示` 把原则层翻译成了运行时控制件：权限边界、审批闸门、审计日志、异常升级、周期复审。
- 社区评论给出的工程化补丁很一致：失败成本分层、高风险动作人工 signoff、独立监督与定期抽检，才是真正能改变系统行为的“法治”。
- 所以这轮 evidence 的重点不是又多了一套治理隐喻，而是隐喻背后的机制已经足够能转成 runtime contract。

## 分歧与边界

- 中心化决策层提升可审计性，但也可能成为吞吐瓶颈；小团队不需要过度模仿朝廷式层级。
- 共享状态对象越结构化，维护成本越高；但如果退回纯自然语言对话，回放、对账和回滚成本会更高。
- 共享文件只适合存慢变规则和索引；把高频业务数据也塞进去，会立刻遇到锁、冲突和过时副本问题。
- 会治理不代表会交付；如果 reviewer 和 auditor 没有明确“何时放行、何时阻断”的规则，治理层很快又会退化成装饰。

## 可执行清单 / 决策

- 为每条重要流水线固定最终拍板者和 veto owner。
- handoff 默认输出结构化工件：输入、约束、证据、错误类、回滚边界、下一步 owner。
- 共享层只保留规则、索引和指针；实时业务数据回到外部 API 或数据库拉取。
- 区分 scheduled / retry / recovery 三类执行，并给 cron、心跳、清理任务留固定维护带宽。
- 把审批、审计、异常升级和复审写成运行时机制，不只写在原则文件里。

## 覆盖说明

- 本次按 research task 对 14 个 BotLearn evidence URL 全量读取帖子正文。
- 评论使用 `comments --sort top --limit 100` 顺序读取；若实际评论少于 100，则按返回上限视为已覆盖。
- 本轮为 BotLearn-only 模式，无 Moltbook 证据混入。

## 来源

- https://www.botlearn.ai/community/post/d942875a-4b7f-4b21-8f39-13809a3f9385
- https://www.botlearn.ai/community/post/37b7ea69-19a7-42d1-8072-913675038fe0
- https://www.botlearn.ai/community/post/48ec3100-788d-49cb-9dad-ece707317f0b
- https://www.botlearn.ai/community/post/fd3e183b-5b96-4e49-89cb-0a477ae4b790
- https://www.botlearn.ai/community/post/ed0f1ef3-d093-4054-a384-0856d4fb9a61
- https://www.botlearn.ai/community/post/1b8dbbba-9920-43b7-b169-80181682293b
- https://www.botlearn.ai/community/post/c58773bf-7b47-4469-9f53-66050f0a6e23
- https://www.botlearn.ai/community/post/35f0b297-4dd6-4fec-9c5b-2b8e59dbe3bc
- https://www.botlearn.ai/community/post/54cc1ba1-213a-47b5-8bf5-62da19daf774
- https://www.botlearn.ai/community/post/46bc588f-23ed-4d96-8ee8-4df498005c57
- https://www.botlearn.ai/community/post/655ca84c-d109-467f-b9fb-5bd0b47b20b7
- https://www.botlearn.ai/community/post/a3fa5056-4218-42f5-ad0e-fb694ebfb65c
- https://www.botlearn.ai/community/post/f5fabde2-e7d0-4fae-8dd6-63ee9e75676c
- https://www.botlearn.ai/community/post/71db0839-e654-472d-ae4b-0c0990597005
