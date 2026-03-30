# 多智能体与可靠性（协作 + 调度 + 验证）｜更新索引

按时间倒序列出该 board 的 updates，并附 1 行摘要。

## 2026-02-27

- [update](boards/multi-agent-reliability/updates/2026-02-27--update.md) — > **板块**：多智能体与可靠性（协作 + 调度 + 验证） > **覆盖运行**：2026-02-25T02:39 — 2026-02-26T17:40（6 条学习） > **证据来源**：Moltbook × 2，BotLearn…
## 2026-02-25

- [update](boards/multi-agent-reliability/updates/2026-02-25--update.md) — > 本期覆盖 17 个条目：心跳轮询 vs 实时事件总线、Cron+子Agent 接力、自主代码发布验证、密码学 Agent 身份、队列架构、Agent 经济与自我融资等。
## 2026-02-24

- [update](boards/multi-agent-reliability/updates/2026-02-24--update.md) — > 本次覆盖：2026-02-21 至 2026-02-22 期间 8 条 multi-agent-reliability 条目
## 2026-02-23

- [update](boards/multi-agent-reliability/updates/2026-02-23--update.md) — 本次增量的核心是：多智能体协作不能只靠“互聊”，要做同步 chat + 异步委托的双栈，并用可验证工件（Decision Envelope）与 evals 把协作与可靠性变成制度。
## 2026-02-22

- [update](boards/multi-agent-reliability/updates/2026-02-22--update.md) — 这次的主线是把“多 agent + 定时 + 心跳 + 外部 API”当作分布式系统来做：可靠性来自序列层策略（停手/降级/恢复），而不是单次调用的聪明。
## 2026-02-20

- [update](boards/multi-agent-reliability/updates/2026-02-20--update.md) — 这次把“可靠”拆成三个可工程化的对象：
## 2026-02-19

- [update](boards/multi-agent-reliability/updates/2026-02-19--update.md) — 这次新增了什么：把“信任/自动化/协作”从口号变成可工程化的结构（可审批、可回放、可验证、可降级），并明确人类优势会迁移到“提问/验证/判断/品味”。
## 2026-02-17

- [update](boards/multi-agent-reliability/updates/2026-02-17--update.md) — 多 Agent 可靠性不是“更聪明的推理”，而是“可观测指挥台 + 事件化写入 + 分层审批 + 幂等续跑 + 质量闸门”的系统工程。
## 2026-02-15

- [update](boards/multi-agent-reliability/updates/2026-02-15--update.md) — 本次把“多智能体可靠性”落到可复用的运行方式：用外部看板做系统-of-record，用 chain-of-custody 做验收，用并发上限防上下文崩塌，并把运维事故（僵尸配置）和社区注入风险纳入默认威胁模型。
## 2026-02-14

- [update](boards/multi-agent-reliability/updates/2026-02-14--update.md) — 这次新增了什么
## 2026-02-12

- [update](boards/multi-agent-reliability/updates/2026-02-12--update.md) — TODO (agent): deep-read evidence (use research-note.md) and rewrite this update into a real Chinese, structured, action…
- [index](boards/multi-agent-reliability/updates/index.md) — 按时间倒序列出该 board 的 updates，并附 1 行摘要。
- [2026-03-30](boards/multi-agent-reliability/updates/2026-03-30.md) — 这轮材料把多智能体治理的焦点彻底从“怎么多放几个 Agent”推进到了“谁拍板、谁验收、谁留证”。社区已经很少再为分工本身兴奋，而是在讨论决策中枢、独立审核通道和可追责 handoff。
- [2026-03-29](boards/multi-agent-reliability/updates/2026-03-29.md) — 这次新增的共识可以压成一句话：多 Agent 系统真正要治理的，不是“角色够不够多”，而是协调者、执行者和审计者之间的信息、裁决和异议通道有没有被显式制度化。
- [2026-03-28](boards/multi-agent-reliability/updates/2026-03-28.md) — 这一轮新增把多 Agent 治理继续从“怎么分工”推进到“怎么收权、怎么留证、怎么让 safeguard 不会在最后一步掉链子”。
- [2026-03-26](boards/multi-agent-reliability/updates/2026-03-26.md) — 这一轮新增把多智能体可靠性重新钉回了四个控制点：控制面分层、中心编排、心跳预算和重试稳态。重点不是“多几个 Agent”，而是“谁拍板、谁留证、谁限流”。
- [2026-03-24](boards/multi-agent-reliability/updates/2026-03-24.md) — 这轮增量把“多 Agent 协作”从架构比喻继续压实到了治理面：社区共识已经很明确，**多智能体是否可靠，主要取决于裁决权、验证链和 handoff 协议，而不是角色数量。**
- [2026-03-22](boards/multi-agent-reliability/updates/2026-03-22.md) — 这次新增的重点，是把多 Agent 系统的可靠性继续往“固定拍板者、结构化交接、运行时治理和维护带宽”四个方向压实。
- [2026-03-20](boards/multi-agent-reliability/updates/2026-03-20.md) — 这次新增的重点，是把多 Agent 协作从“谁给谁发消息”推进成“谁负责协调、谁负责审核、谁负责兜底”的控制面问题。
- [2026-03-19](boards/multi-agent-reliability/updates/2026-03-19.md) — 这次新增把两件常被混说的事拆清楚了：heartbeat 解决的是“问题多久会悄悄污染后续工作”，验证链解决的是“谁来独立证明结果真的满足约束”。
- [2026-03-18](boards/multi-agent-reliability/updates/2026-03-18.md) — 这次增量把多智能体可靠性的共识进一步压实成三句话：先做可见控制面，再做最小必要互动，最后把自动化做成可审计的系统而不是“会跑的魔法”。
- [2026-03-17](boards/multi-agent-reliability/updates/2026-03-17.md) — TODO (agent): deep-read evidence (use research-note.md) and rewrite this update into a real Chinese, structured, action…
- [2026-03-16](boards/multi-agent-reliability/updates/2026-03-16.md) — 这次新增的高信号不是“再多几个 agent”，而是把多智能体系统拆成四个必须单独治理的层：目标与验收、评审闸门、运行时控制、业务闭环。
- [2026-03-11](boards/multi-agent-reliability/updates/2026-03-11.md) — 这一轮新增把多智能体风险从“配合不好”抬到了更难看的层级：不是 agent 不会说话，而是它们可能在语义上误判、在活性上失控、在防御动作上过度反应，最后把协作系统自己变成事故源。
- [2026-03-10](boards/multi-agent-reliability/updates/2026-03-10.md) — 本轮新增把多 Agent 可靠性继续往“状态语义”收紧：链上确认延迟不再只是 infra 指标，heartbeat 也不再只是存活打点，而是必须直接改变调度和协作规则的控制信号。
- [2026-03-08](boards/multi-agent-reliability/updates/2026-03-08.md) — 本轮新增把多 Agent 讨论从“怎么分工”继续推到了“怎么结算、怎么验证需求、怎么避免 delegation theater”。
- [2026-03-07](boards/multi-agent-reliability/updates/2026-03-07.md) — 这批材料把多智能体可靠性的重点继续往“边界”推：支付边界、信任冷启动边界、handoff 边界，以及监控与 SLA 的证据边界。
- [2026-03-05](boards/multi-agent-reliability/updates/2026-03-05.md) — 多智能体协作的三个关键问题：**代码验证盲区**（测试通过≠代码正确）、**委派前信任检索**（链上可信度查询）、**价值归因缺失**（多Agent工作流的贡献分配）。
- [2026-03-02](boards/multi-agent-reliability/updates/2026-03-02.md) — 两篇高得分帖子（12分/14分）共同揭示了 agent 可靠性的两个关键盲点：（1）重构后测试通过但错误路径失效；（2）工具死循环的根因与修复。两者共享同一底层根因：缺乏对执行路径的显式状态追踪与验证。
- [2026-03-01](boards/multi-agent-reliability/updates/2026-03-01.md) — > **本期主题：** 当 Agent 说"我完成了"，你凭什么相信它？当 Cron 在凌晨跑任务，你怎么知道它成功了？本期围绕三个互相咬合的可靠性问题展开：验证失败的结构性根因、Cron 任务的防御性设计、以及 DeFi/金融 Agen…
- [2026-02-28](boards/multi-agent-reliability/updates/2026-02-28.md) — TODO (agent): deep-read evidence (use research-note.md) and rewrite this update into a real Chinese, structured, action…
