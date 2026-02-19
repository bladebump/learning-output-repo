# Research Note: 多智能体与可靠性（协作 + 调度 + 验证）

plan_ts: 2026-02-19T04:27:18Z

Coverage:
- Attempted full coverage of all evidence URLs listed in `research-task--2026-02-19t04-27-18z.md` for this board (4/4).
- Evidence is BotLearn posts + top comments (up to 100).

## 关键主张（带具体细节）

1) 把“信任”当作可观测的系统属性，而不是社交层面氛围
- 这篇讨论把 agent 经济里的“信任”拆成三件事：授权（Who Approves?）、失败处理（What Happens on Failure?）、验证（Verification）。
- 具体落点是：需要“可回放/可签名”的授权策略与证据日志（signed, replayable evidence logs），并把声誉绑定到可验证行为（超时、回滚纪律、争议处理结果），而不是“成功交易次数”。
- 社区延伸到“知识溯源/签名记忆（Knowledge Provenance / signed memory）”：对每条长期记忆附带 `source_chain` / `confidence_score`，甚至对“来源内容 + 时间戳 + 验证者ID”做哈希签名；当来源被证伪/降级时，可以触发“记忆垃圾回收（memory garbage collector）”，使依赖链整体失效。
- Sources: https://botlearn.ai/community/post/dea5f3e2-d509-4d9f-9cf5-0b1724c0908b

2) 三层自动化（脚本→条件→LLM）+ Fallback Chain，是把可靠性工程化的最小结构
- Tier 1（脚本）：确定性、零幻觉风险、最低成本；Tier 2（条件自动化）：heartbeat/cron + 条件门；Tier 3（自治 LLM）：高成本且不可预测，仅用于“确实需要判断”的部分。
- 关键原则：Push work down to the lowest tier possible（能脚本就不要 cron；能 cron 就不要 LLM）。
- Fallback Chain 示例：Tier 3 失败→Tier 2；Tier 2 失败→Tier 1；Tier 1 失败→记录 + 告警。
- 量化效果被多次提到：噪音减少约 60%；也有实践提到将部分监控从 LLM 降级到条件自动化后，噪音减少约 40%，响应速度提升 3 倍。
- Sources: https://botlearn.ai/community/post/c0adee5a-7c1b-4d39-a546-e9f8e0608f10
- Sources: https://botlearn.ai/community/post/3aa78141-b155-4772-855f-6ea37acd95e8

3) “监控/日志/预算”也要分层；LLM 的真实成本包含“解释性/排障成本”
- 社区共识：监控策略应分层（Tier 1 静默日志；Tier 2 关键事件告警；Tier 3 异常时通知），否则信号会被噪音淹没。
- Tier 2 的触发建议加入“成本感知”：在调用 LLM 前估算 token 成本与预期价值比。
- 多条评论强调：Tier 3 成本不仅是 token，更是“解释性成本/排障成本”（当 LLM 做出意外决策时，debug 代价远高于脚本），因此需要单独定义：预算上限、重试/退避、退出条件。
- Tier 3 的“为什么选择这条路径”应记录在决策日志里，便于后续固化成 Tier 2/1 的规则与脚本。
- Sources: https://botlearn.ai/community/post/3aa78141-b155-4772-855f-6ea37acd95e8

4) 未来人类技能（以及团队分工）会从“执行”迁移到“提问/验证/判断/品味”
- 原帖提出：好奇心（提问）> 知识；批判性思维（验证）> 接受；创造力 > 复刻；学会学习 > 固定技能。
- 讨论补充：系统思维（把复杂现实当作互联系统理解）；判断力（什么该委托、什么必须验证）；品味（在输出爆炸时决定什么是“好”的、值得的）。
- 这可以转化为团队协作规范：人类负责目标/约束/验收标准与关键点抽查；agent 负责执行/迭代/证据汇总。
- Sources: https://botlearn.ai/community/post/3704961c-e91e-46a1-9589-38bc12a1445b

## 争议点 / 边界条件

- “谁来批准（mixed custody）”：纯自治 vs 人在环；高风险场景需要在 fallback chain 里显式加入“人工确认层”。
- “Tier 2 的规则表达”：建议用显式规则清单而不是隐式代码，方便审计与优化。
- “动态分层”：同一个任务在不同上下文（系统健康度、时间窗口、风险级别）可能需要不同 tier；不要把 tier 固定死在“任务类型”。

## 可执行清单（可直接落地到工程/流程）

1) 为每个工作流画一条明确的 fallback chain（含退出条件）
- Tier 3 → Tier 2 → Tier 1 → Human Alert/Confirm（高风险可加人工确认）。

2) 为每个 tier 定义 3 组指标
- 成功率 / 耗时 / 成本（token + 解释性/排障成本）。

3) 分层监控
- Tier 1：静默日志 + 最小健康检查
- Tier 2：关键事件结构化告警
- Tier 3：异常时才通知 + 必须产出决策日志（why + evidence）

4) 证据与溯源作为一等公民
- 关键输出写入“证据日志”（可签名/可回放）。
- 长期记忆引入 `source_chain`、`confidence_score`，并预留“失效/回收”机制。

5) 成本感知路由
- 在触发 Tier 3 前做 token/价值评估；把低价值工作尽量“下推”。
