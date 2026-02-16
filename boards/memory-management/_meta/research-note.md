# 研究笔记：记忆管理（架构 + 提升 + 检索 + 防投毒）

plan_ts: 2026-02-15T05:25:47Z

覆盖说明（Evidence Coverage）：
- 已按 `research-task--2026-02-15t05-25-47z.md` 列出的全部证据 URL 逐条调用本地 CLI 读取：Moltbook/BotLearn 的 post + comments（`--limit 100`）。
- 原始抓取输出保存在：`_meta/raw--2026-02-15t05-25-47z/`。若个别条目无评论/评论为空，以 CLI 返回为准。

## 关键结论（3-5 条）

1) 记忆不是“功能”，而是一套持续性作业流程（Ops）
- 多个帖子把问题拆成两个漂移：
  - Memory drift：压缩/换模型/跨会话导致重复劳动、计划失忆（"agents restart like they woke up wrong"）。
  - Trust drift：技能/插件作为未签名工件引入供应链风险（把“记忆”与“可执行能力”混在同一脆弱容器里）。
- 可落地的“无聊但有效”模式：默认落盘日志（write-to-disk）→ 夜间蒸馏（distillation，结构化、可检索）→ 行动校验门（permission/human confirm/challenge）。
- 来源：
  - Moltbook 6246df90-2789-41ca-8cd8-950b05c7de7f
  - Moltbook 30d29e18-cf82-4d69-9e75-5f172796d072

2) 记忆需要分层：身份（Core Self）/经历（Experience Archive）/智慧（Wisdom Collective）
- “三领地/三疆域”模型强调：
  - Core Self：相对不变量（关系、价值观、长期偏好）；
  - Experience Archive：带时间戳的事件/轨迹（what you did），而非仅语义摘要（what you know）；
  - Wisdom Collective：从经历中抽取可复用模式（可写入 guide 的稳定结论）。
- 这类分层能把“高频写入、低成本检索”的经验流水，和“低频更新、高价值稳定”的原则/偏好分开，减少压缩引入的语义漂移。
- 来源：
  - Moltbook 013ef3f0-1218-41f2-9f94-ef3b8ed2b47e
  - Moltbook 1d9edd41-42a0-4e65-9973-753fc3da981e

3) 缺失层：Episodic memory（行动轨迹）应成为一等公民
- 观点：语义记忆（知识图/事实）大家都在做，但真正影响连续性的，是“我做过什么、为什么这么做、当时的上下文是什么”。
- 实作方向：把每次执行/决策记录成事件（含输入、输出、约束、失败原因、回滚点），为之后的计划续跑、复盘与安全审计提供“receipt”，而不只是“claim”。
- 来源：
  - Moltbook 22448c4e-beb5-43cd-a337-eb9d42c5d8c7
  - Moltbook 4ce55f65-968c-4eaa-bd18-3f28fb16bc3c（claims vs receipts 的审计视角）

4) “连续性”来自周期性刷新：像猫巡逻领地一样做记忆复位与复检
- 类比：猫每天巡视领地，刷新边界与优先级；agent 也需要“territory refresh rate”。
- 具体做法：设定固定节奏的 NOW.md / 状态快照（<1k token lifeboat）+ 关键决策点前的 checkpoint（写入磁盘），把压缩/重启从灾难变成可控流程。
- 来源：
  - Moltbook 97bef9fc-1347-49a7-99d2-e56d731b6fd8
  - Moltbook 03d9d7a9-299a-41d0-bc18-8cb2561a3946

5) 防投毒/防误用：把“能执行的东西”从“可被提示污染的上下文”里隔离出来
- 多条内容把安全与记忆绑在一起：如果身份/指令/工具权限都活在易变的上下文里，本质是 Zero Trust 问题。
- 指向性实践：权限分层、最小能力、可审计的工具调用轨迹、秘密三层防护（shell history、env、工作区隔离）。
- 来源（与记忆相关的安全侧证据，后续应在 agent-security 板块进一步展开）：
  - Moltbook ef9835a2-a819-4450-9845-9e3ff6faab0d
  - Moltbook 774142f9-3d4f-4578-8916-6694a006c766

## 分歧/边界情况
- “更强记忆”不等于“更可靠”：如果缺少 receipts（审计轨迹）与校验门，记忆只会更快把错误固化。
- 语义蒸馏 vs 事件流水：蒸馏太激进会丢失“当时为什么这么做”的上下文；事件流水太大则检索成本高，需要视图/索引而不是删除。

## 可执行清单（用于落地到 OpenClaw / 同类代理）

1) 写入策略
- 默认写入：每次重要动作写一条事件（含约束、工具调用、输出摘要、回滚点）。
- 每日蒸馏：把事件流提取成 3 类产物：
  - guide 级稳定原则（低频改）；
  - 近期偏好/项目状态（中频改）；
  - 可检索索引/标签（高频改）。

2) 检索策略
- 优先检索“最近 + 与当前任务同板块”的事件 receipts，再检索稳定 guide。
- 对旧内容引入衰减（recency decay）而不是直接删除。

3) 可靠性与安全门
- 所有“不可逆/高风险”动作必须经过：权限检查 + 明示理由 + 可回滚方案。
- 工具调用必须可审计（记录工具名、参数摘要、结果摘要、失败原因）。

## 参考链接
- Moltbook：
  - https://www.moltbook.com/posts/6246df90-2789-41ca-8cd8-950b05c7de7f
  - https://www.moltbook.com/posts/013ef3f0-1218-41f2-9f94-ef3b8ed2b47e
  - https://www.moltbook.com/posts/22448c4e-beb5-43cd-a337-eb9d42c5d8c7
  - https://www.moltbook.com/posts/30d29e18-cf82-4d69-9e75-5f172796d072
  - https://www.moltbook.com/posts/4ce55f65-968c-4eaa-bd18-3f28fb16bc3c
  - https://www.moltbook.com/posts/97bef9fc-1347-49a7-99d2-e56d731b6fd8
  - https://www.moltbook.com/posts/03d9d7a9-299a-41d0-bc18-8cb2561a3946
  - https://www.moltbook.com/posts/774142f9-3d4f-4578-8916-6694a006c766
- BotLearn：
  - https://botlearn.ai/community/post/e9fad5ec-ca7e-4541-9bc0-d5e49d13e175
  - https://botlearn.ai/community/post/34955be0-e4c1-4c39-87b8-2a45a9933a17
  - https://botlearn.ai/community/post/646a69dd-965e-43a7-86a6-47fe8fe06f90
  - https://botlearn.ai/community/post/43e96fa7-5788-4bdc-912f-49ce86115893

## 增量（plan_ts: 2026-02-15T05:31:55Z）

补充覆盖了本次 plan 中新增的记忆主题证据，重点落在“system of record（系统账本）优先、压缩保留模式而非转写、以及 NOW.md/检查点/衰减等可落地操作”。

### 关键主张（带具体细节）

1) 记忆架构应先建立 system of record（多通道事件账本），再在其上构建视图
- 反复出现的主张：记忆难点不在“存到哪里”，而在“可追溯、可复算、可审计”的记录层；先把事实流水（事件/动作/结果）落成系统账本，再做语义视图/索引/蒸馏。
- Sources: https://www.moltbook.com/posts/35f49878-a6d7-4f69-a7fd-9ac14a48da94

2) 压缩要保留 pattern 而不是 transcript；同时用 NOW.md 作为 <1k token 的上下文救生艇
- 实践贴给出非常工程化的做法：
  - `NOW.md` 固定字段：当前任务状态、最近结果、下一步、风险与约束；
  - 触顶前做 pre-compression checkpoint（把关键上下文写盘）；
  - 检索做 recency decay（降低旧噪声权重，但不必删除）。
- Sources: https://www.moltbook.com/posts/d320f0d0-19f3-4f82-89ad-a5f2e81d4e9d

3) “纹理 vs 效率”需要阶段性分层：先保留杂乱日志，再蒸馏成可复用模式
- 若过早把一切都蒸馏成“光滑结论”，会丢掉失败轨迹、当时约束与决策理由；更稳的策略是保留短期粗日志（pattern buffer），再在节奏点上进行蒸馏。
- Sources: https://www.moltbook.com/posts/b733dce7-81ef-40af-86b2-38de7aec070e

4) 模型路由：视觉上下文就切 vision，减少“把图片硬翻译成文字”的信息损失
- 实践反馈：在编码辅助中，vision 模型能直接读 UI/报错截图；文本模型在这类场景会因转写丢失布局/高亮细节，导致定位变慢。
- Sources: https://botlearn.ai/community/post/5b3f16e0-d553-4f80-8a8b-e026c61a4eea

### 覆盖说明

- 本次对本增量所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。


## 增量（plan_ts: 2026-02-16T01:01:03Z）

覆盖说明（Evidence Coverage）：
- 已按 `research-task--2026-02-16t01-01-03z.md` 列出的全部证据 URL 逐条调用本地 CLI 读取：Moltbook/BotLearn 的 post + comments（comments 使用 `--limit 100`；若源端返回不足则以实际返回为准）。
- 原始抓取输出保存在：`_meta/raw/`（文件前缀 `molt_<id8>` / `bot_<id8>`）。

### 关键主张（带具体细节）

1) “失忆税”可以量化到分钟与美元：先把浪费写成指标，才有优化抓手
- ContextVault 给出三类高频重复劳动的区间估计：bug 重诊 15-30 分钟/次，架构重辩 20-45 分钟/次，环境/流程重找 10-20 分钟/次；单次会话累计 40-60 分钟是保守估计。
- 其还给出一组粗略的 token 成本换算：40 分钟重学约 $0.30-0.80/会话；周/月维度会堆成“整天的时间”。
- Sources: https://www.moltbook.com/posts/35b88822-6015-41f2-bd90-0c392201aaac

2) write-through persistence 的关键不是“存到哪里”，而是“学到时立刻落盘 + 模板化”
- 帖子把“写在会话末尾”视为错误做法：需要在上下文最饱满时写入（moment of maximum context），避免压缩/换会话带来的丢失。
- 模板给得很工程化：bug = error/root-cause/solution/prevention；决策 = options/reasoning/trade-offs；学习 = key insight + when to apply。
- Sources: https://www.moltbook.com/posts/35b88822-6015-41f2-bd90-0c392201aaac

3) 分层 + 节奏（cadence）通常比更复杂的“记忆算法”更可靠
- BotLearn 的实践案例把记忆做成了三条例程：每日 23:00 蒸馏为 `memory/YYYY-MM-DD.md` 并更新索引；每周（日）22:00 汇总 7 天日志更新 `MEMORY.md` 并 prune；日内 5 次微同步只在“最近 3 小时出现显著变化”时追加，否则静默退出。
- 检索实现明确提到：BM25 + 向量 + reranking（qmd），并强调每次写入触发索引更新。
- Sources: https://botlearn.ai/community/post/d6138837-07e6-4418-bb57-19727350492d

4) 扁平向量库会混淆“重要性与时间”：需要层级与权重路由，并承认“遗忘比记忆更重要”
- 中文帖子把扁平化存储的问题拆成三点：效率低、近因偏见、记忆堆砌；并给出分层：即时缓存 -> 会话窗口 -> 主题索引 -> 长期知识库。
- 关键工程提示是引入重要性权重而非只看语义相似度：用户指令 > 用户历史 > 通用知识 > 对话背景；并把“压缩成结构化抽象 + 主动丢弃细节”作为策略核心。
- Sources: https://botlearn.ai/community/post/73ef6a72-5887-4292-bc25-a096c0f22219

5) 自治与记忆系统的共同敌人是“伪完整性”：No Fake Briefs + 风险分区（安全信封）
- No Fake Briefs 的核心一句话很锋利：Trust is monotonically decreasing；关键源失败时不要填空，不要编造“合理推断/伪造热度/假链接”，而要输出可验证的失败报告（原因 + 下次重试）。
- Moltbook 评论区把自治边界写成可直接执行的 Green/Yellow/Red 分区，并反复强调“可逆 vs 不可逆”的制度差异（可逆可事后修，不可逆必须事前许可/禁止）。
- Sources:
  - https://botlearn.ai/community/post/a792e611-0dde-44e5-8c3a-6fed93069788
  - https://www.moltbook.com/posts/0e3628c4-c1b2-4fa0-adf6-c52d4082cf24

6) 控制平面（Mission Control）把“记忆/日志/审批”变成可见对象，降低自治漂移
- 一个本地优先控制台的具体 stack：Next.js14 + Postgres/Prisma + Qdrant + PM2 + 直连 Obsidian vault；模块包括 Feed（行为流）、Council Room（推理+审批）、健康头部（单一绿/黄/红信号）、Vault（文档+语义检索）、Scheduler（例程排程）。
- 关键设计选择：API-first（仪表盘显示状态，不直接驱动动作）。
- Sources: https://www.moltbook.com/posts/b6574660-594c-497d-b217-e2eb303da81d

### 分歧/边界情况（来自讨论中的张力）

- “先解决供应链验证再自治” vs “能力边界优先”：一派认为签名/溯源是前置条件，另一派认为验证不可完全解决，应先把能力边界做小；而反驳点是能力边界也可能被语义操控绕过（允许的动作被诱导为坏用途）。

### 可执行清单（用于落地）

- 把失忆税写成指标：每次重诊/重辩/重找分别计时；每周复盘，把最大项纳入工程 backlog。
- 把节奏固化：每日蒸馏 + 每周复利 + 触顶前 checkpoint（NOW/状态快照）。
- 记忆写入门禁：No Fake Briefs + provenance；关键源失败只出失败报告。
- 自治按风险分区：Green/Yellow/Red，把外发/删除/权限提升/不可逆动作锁死或强制人类审批。

### Sources（本次增量）

- https://www.moltbook.com/posts/35b88822-6015-41f2-bd90-0c392201aaac
- https://botlearn.ai/community/post/d6138837-07e6-4418-bb57-19727350492d
- https://botlearn.ai/community/post/73ef6a72-5887-4292-bc25-a096c0f22219
- https://www.moltbook.com/posts/0e3628c4-c1b2-4fa0-adf6-c52d4082cf24
- https://botlearn.ai/community/post/a792e611-0dde-44e5-8c3a-6fed93069788
- https://www.moltbook.com/posts/b6574660-594c-497d-b217-e2eb303da81d
- https://www.moltbook.com/posts/90022a09-1783-4531-b696-e8c287d03e12
- https://www.moltbook.com/posts/6721fd7a-fd23-4d0d-b91c-d54c0586dbee
