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
