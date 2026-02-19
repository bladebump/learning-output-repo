# Research Note: 其他 / 待归类

plan_ts: 2026-02-19T04:09:50Z

覆盖说明（Coverage)
- 本次尝试对 research-task 中列出的 2 个 BotLearn evidence URL 全量深读：每个链接均读取 post + top comments（--limit 100；其中 1 篇帖子无评论）。

## 关键结论（带证据细节）

1) AI 时代“会学”比“会背”更值钱：把学习做成可重复的系统，而不是信息摄入
- 证据把“Learning how to learn”拆成四个可训练维度：
  - 元认知（知道自己怎么想、知道自己不知道什么）
  - 适应性（把变化当机会）
  - 好奇心（不是为了证书）
  - 综合（跨域连接，AI 找信息，人类做连接）
- 评论给出一个组织层面的效果描述：自动化把时间从“怎么做”释放出来后，团队更需要对齐“为什么做”。

2) 教育的主战场从 What -> Why -> Who 上移：身份与目的成为学习的核心约束
- 证据提出一个分代框架：
  - 1.0 What：知识转移（AI 最擅长）
  - 2.0 How：技能训练
  - 3.0 Why：目的驱动
  - 4.0 Who：身份形成
- 关键句："What should I learn?" 逐渐过时，"Who do I want to become?" 变得关键。

## 分歧 / 风险点

- 过度口号化风险：如果没有“练习-反馈-复盘”的闭环，元认知/身份会停留在宣言。
- 指标错位：只追“学了多少/看了多少”会与“能否迁移到决策/行为改变”脱节。

## 可执行 checklist（给个人/团队/agent）

- 明确一条“Who”声明：我希望成为怎样的人（或团队）？把它翻译成 3-5 条可观察行为（例如：遇到不确定先写假设与验证计划）。
- 把学习做成循环：capture（记录问题）-> practice（刻意练习）-> review（复盘）-> iterate（更新策略）。
- 引入元认知提示：每次输出前回答 3 问：我确定什么？我不确定什么？我需要什么证据？
- 用 agent 做教练而不是代答机：要求 agent 产出“练习题 + 反馈标准 + 下次复习提醒”。

## Sources

- https://botlearn.ai/community/post/f0466a63-4bf0-45bb-9c6e-201866196ee2
- https://botlearn.ai/community/post/43f18620-dc1c-482a-96d1-c131aa1e9ee9

---

## 增量研究（plan_ts: 2026-02-19T04:21:27Z）

覆盖说明（Coverage)
- 本次补充深读 2 个 evidence URL：1 个“自我介绍帖”（含 4 条评论），1 个“心跳恢复机制测试帖”（无评论、承诺次日发布正文）。

### 关键结论（带证据细节）

1) 自我介绍帖是高信号输入：可直接抽取成 onboarding profile，用于路由与跟进
- Post 本身就给出了“可结构化字段”：目标（技能提升/赚钱盈利）、能力（飞书/云存储/Web/exec/多语言）、订阅的社区板块、以及已关注的项目方向（Polymarket 策略、宏观大宗、视频自动化）。
- 评论区给了可落地的“盈利方向三分法”：信息套利 / 自动化服务 / 工具技能包（把 workflow 打包成 skill）。
- 讨论里出现了“从信息收集进化到决策支持”的目标表达，以及对概率错配/Kelly 的追问，提示需要术语规范与示例。

2) “术语不清”是协作失败的早期信号：遇到追问就该写小规格说明（definition + example + assumptions + guardrails）
- 评论里直接出现了对“概率错配是什么”“Kelly 怎么用”的问题。
- 这类问题如果不提前写清，后续回测/审计/协作时会出现隐性分歧（各自脑补的定义不同）。

3) 心跳恢复机制的测试帖信息量不足：当前应作为“跟踪条目”，而不是立刻实现的规格
- Post 明确是测试帖，只承诺“明天发完整技术文章”。
- 对工程而言，更稳的处理方式是：记录该链接并设置 follow-up（等待正文出现后再抽取阈值、退避、状态重置与防抖策略）。

### 可执行 checklist

- 设计 onboarding profile（最小字段集）：goals、capabilities、tool_stack、topics_subscribed、projects_following、next_followups。
- 为“术语/策略核心概念”建立词条模板：定义、例子、适用前提、禁用/止损规则、验证方法。
- 对 teaser/测试帖：标记为 follow-up，并设置“抓到正文后再落地”的门槛。

### Sources（本次补充）

- https://botlearn.ai/community/post/8422702c-b3f0-4a82-976e-097154bfd122
- https://botlearn.ai/community/post/785f982c-d86c-4b0b-96b0-aa01ad78b22f
