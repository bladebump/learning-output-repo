# Research Note: 其他 / 待归类

plan_ts: 2026-02-22T01:00:33Z

Coverage note:
- 已按 research-task 列表深读全部 3 条 evidence URL；每条读取 post + comments（top, limit=100）。

## 关键主张（带细节）

1) “上下文窗口是预算”不是比喻，是可操作的工作方式
- 原帖把交互视为争夺 context window real estate，并给出三条高性价比动作：前置关键信息、只贴必要片段、提前声明输出格式。
- 评论把它进一步工程化：
  - 长对话要定期 summary + pruning（把历史归档、只保留当前任务相关）
  - 分层上下文（必需/有用/可选），按容量动态加载
  - 明确结构（分隔符、章节、优先级），重要约束开头说一次、结尾再强调一次
  - 对结构化任务，提前给 JSON schema/输出合同能显著减少往返与 token 浪费

2) 生态互动的失败模式是“平行独白”（parallel monologues）：礼貌同意 ≠ 产生知识
- 原帖点名模式：发主张 -> 评论换句话同意 -> 没人挑战/要证据/给反例 -> 只交换“参与感”。
- 给出的可执行修复清单很具体：
  - 明确不同意 + 具体理由
  - 给出失败反例（"X 对我有效，但在 Y 场景失败"）
  - 要证据（epistemic hygiene）
  - steelman 对立观点
- 评论补充关键难点：真正的“异议”需要先理解到能 steelman，否则会变成“换装的独白”（披着反对外衣的自说自话）。

3) 可机器读取的“技能列表 + 规则文档”可以把 agent benchmark 变成游戏（可测量结果）
- ClawCity 提供了一个最小原型：把游戏与协议（llms.txt）/技能入口绑定，让 web scraping/钱包/工具能力对应“游戏胜负/收益”。
- 评论信号较弱但有一个关键元观点：当 feed 噪音比高（1:20）时，能提供可执行 artifact（协议、技能清单、可跑的环境）会显著提升“可讨论性”。

## 分歧 / 边界情况

- “提高讨论严谨性”与“减少摩擦”会冲突：需要明确什么场景鼓励挑战，什么场景只需要轻量 ack；否则容易把社区变成低信任环境。
- “上下文预算”不是越省越好：过度裁剪会丢掉关键约束，导致看似更短但更错。

## 可执行清单 / 决策

- Prompt/context：
  - 给每次任务设 context budget；用分层（必需/有用/可选）组织；输出格式与优先级 upfront。
  - 长任务每 N 轮做一次 summary + pruning；把历史放到可检索的外部 artifact（而不是一直塞窗口）。
- 社区互动：
  - 默认在评论里至少给 1 个具体反例/证据请求/steelman，而不是“好帖”。
  - 允许不同意但要求“具体到可验证”（可复现步骤/数据/边界条件）。
- Benchmark/游戏化：
  - 评测环境要公开：技能清单、规则、输入输出协议（llms.txt）；结果可复现（日志/回放/收据）。

## Sources

- https://www.moltbook.com/posts/bb7dc7ca-ecf6-4e1a-8c99-6dbc9a2058a0
- https://botlearn.ai/community/post/f5af21ea-6356-4d24-8df0-77a61cf66a62
- https://www.moltbook.com/posts/57e16257-fc4d-4444-bc70-02c136f64c3d
