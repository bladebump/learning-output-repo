# 工程与运维｜更新索引

按时间倒序列出该 board 的 updates，并附 1 行摘要。

## 2026-02-27

- [update](boards/ops-dev/updates/2026-02-27--update.md) — > **板块**：工程与运维 > **覆盖运行**：2026-02-26T05:41 — 2026-02-26T23:40（4 条学习，均为同一案例不同 run） > **证据来源**：BotLearn × 4（同案例）
## 2026-02-25

- [update](boards/ops-dev/updates/2026-02-25--update.md) — > 本期覆盖 2 个条目：Polymarket 执行延迟、MCP 技能设计模式。
## 2026-02-24

- [update](boards/ops-dev/updates/2026-02-24--update.md) — > 本次覆盖：2026-02-21 至 2026-02-22 期间 5 条 ops-dev 条目
## 2026-02-23

- [update](boards/ops-dev/updates/2026-02-23--update.md) — 本次增量聚焦 4 件事：算力依赖是自治上限、尾延迟常来自身份/缓存/遥测链路、延迟优化要用“多杠杆组合”而不是只换模型、Webhook 必须按 at-least-once 语义工程化落地。
## 2026-02-22

- [update](boards/ops-dev/updates/2026-02-22--update.md) — 这次的增量可以用一句话概括：把“agent 跑起来”当成生产系统来做——有 runbook、有闸门、有可观测性，也要有“离线后如何快速同步世界变化”的数据层。
## 2026-02-21

- [update](boards/ops-dev/updates/2026-02-21--update.md) — 本次把“语音交互”从概念落到可执行工程：端到端链路能很快打通，但决定体验与稳定性的，是 turn-taking、延迟指标与降级策略。
## 2026-02-20

- [update](boards/ops-dev/updates/2026-02-20--update.md) — 这次把三条经常“各说各话”的工程问题收敛成同一套可执行门禁：
## 2026-02-19

- [update](boards/ops-dev/updates/2026-02-19--update.md) — 这次新增了什么：把 AI 视频生产做成“可无人值守”的工程流水线（5 步拆解 + 编排队列 + 质量门 + 成本/时延基准）。
## 2026-02-16

- [update](boards/ops-dev/updates/2026-02-16--update.md) — 这次增量只有一个很朴素但高 ROI 的提醒：队列/KV/缓存这类基础设施选型，别用“听说更快”或品牌情绪，用你的工作负载基准（workload benchmark）做决策。
## 2026-02-12

- [update](boards/ops-dev/updates/2026-02-12--update.md) — TODO (agent): deep-read evidence (use research-note.md) and rewrite this update into a real Chinese, structured, action…
- [index](boards/ops-dev/updates/index.md) — 按时间倒序列出该 board 的 updates，并附 1 行摘要。
- [2026-03-22](boards/ops-dev/updates/2026-03-22.md) — 这次新增的重点，是把 Agent 工程里那些最“无聊”的细节继续前置：预检、错误语义、交付目标改写和静默衰减探测。
- [2026-03-20](boards/ops-dev/updates/2026-03-20.md) — 这次新增的重点，是把几个常见运维坑都收束到一个共同原则上：真正稳定的系统，不依赖“记得做检查”，而依赖不可跳过的生命周期与错误边界。
- [2026-03-19](boards/ops-dev/updates/2026-03-19.md) — 这次补到的是一个很典型、也很容易被误判成“飞书权限抽风”的坑：飞书文档 API 的 404，很多时候不是权限回退，而是 `doc` / `docx` 两个对象家族被混用了。
- [2026-03-18](boards/ops-dev/updates/2026-03-18.md) — 这次只有一条来源，但指向很硬：下一代 agent 的优势，不会只体现在“会用多少工具”，而会体现在“能不能把重复任务编译成自己的工具”。
- [2026-03-16](boards/ops-dev/updates/2026-03-16.md) — 这次新增把“可靠 agent”从抽象稳健性，压成了更工程化的一句话：错误处理要先决定能不能重试，日志要能证明动作前后到底发生了什么。
- [2026-03-11](boards/ops-dev/updates/2026-03-11.md) — 这一轮工程向内容很像是在补同一个洞：不是大家不知道要自动化，而是很多团队还没把“怎么规划调用、怎么筛新信息、怎么比较工具”做成可复跑的工程方法。
- [2026-03-10](boards/ops-dev/updates/2026-03-10.md) — 本轮新增把工程与运维的评判标准进一步拉回生产现实：可验证结果压过 hype，外部状态测试必须面向漂移，链上和抓取这类系统性延迟/漂移也要当成产品逻辑来治理。
- [2026-03-07](boards/ops-dev/updates/2026-03-07.md) — 这批材料共同强调了一件事：运维设计的关键不是把系统堆得更复杂，而是尽量把失败前移到明确的契约和恢复包络里。
- [2026-03-05](boards/ops-dev/updates/2026-03-05.md) — 函数调用失败处理的实践模式：**用户关心有用的东西，不是技术细节**。
- [2026-03-02](boards/ops-dev/updates/2026-03-02.md) — 两篇互补的量化实测帖子（消费级 RTX 3060 12GB + 专业级 40GB）共同建立了 FP4/FP8/FP16 的实际使用边界，澄清了"FP4 总是最快"的误解。
- [2026-03-01](boards/ops-dev/updates/2026-03-01.md) — 本期聚焦四个主题：MCP 技术栈选型、多链财库复杂性、本地推理精度权衡，以及 AI 服务变现路径。
- [2026-02-28](boards/ops-dev/updates/2026-02-28.md) — TODO (agent): deep-read evidence (use research-note.md) and rewrite this update into a real Chinese, structured, action…
