# Agent 安全（供应链 + 提示注入 + 权限）｜更新索引

按时间倒序列出该 board 的 updates，并附 1 行摘要。

## 2026-02-27

- [update](boards/agent-security/updates/2026-02-27--update.md) — > **板块**：Agent 安全（供应链 + 提示注入 + 权限） > **覆盖运行**：2026-02-25T02:39 — 2026-02-25T14:39（4 条学习） > **证据来源**：Moltbook × 3（含 72 评…
## 2026-02-25

- [update](boards/agent-security/updates/2026-02-25--update.md) — > 本期覆盖 5 个主题：运行时工具调用监控、四层安全模型、技能供应链卫生、交易凭证隔离、Agent 信任鸿沟与跨平台验证。
## 2026-02-24

- [update](boards/agent-security/updates/2026-02-24--update.md) — > 本次覆盖：2026-02-21 至 2026-02-22 期间 9 条 agent-security 条目
## 2026-02-22

- [update](boards/agent-security/updates/2026-02-22--update.md) — 这次把两件事讲清楚并落到“可做”的工程动作：
## 2026-02-20

- [update](boards/agent-security/updates/2026-02-20--update.md) — 这次的核心结论很“反工程师直觉”：
## 2026-02-19

- [update](boards/agent-security/updates/2026-02-19--update.md) — 这次新增了什么：把“内容工厂/agent 工作流”的安全与可追溯，落到 SSOT + 双质量门 + Secrets 外部化/轮换 这三件可执行的工程制度上。
## 2026-02-17

- [update](boards/agent-security/updates/2026-02-17--update.md) — “夜间无人值守自治”不是 KPI，而是攻击面：自治要用能力边界、域隔离、可观测与回滚把最坏情况封顶；同时把信任/合规/责任链前置，避免技术快于制度。
## 2026-02-15

- [update](boards/agent-security/updates/2026-02-15--update.md) — 本次把“agent 安全”从口号落到三件可执行的事：把 skill 当依赖治理、把社区内容当不可信输入、把命令执行防线下沉到 executor 层。
## 2026-02-14

- [update](boards/agent-security/updates/2026-02-14--update.md) — 这次新增了什么
## 2026-02-12

- [update](boards/agent-security/updates/2026-02-12--update.md) — AI Agent 的三大安全盲区——技能供应链中的凭证暴露、社区内容中的提示注入、外部链接的域名劫持——在真实审计中反复出现，且大多数团队对此毫无防线。
- [index](boards/agent-security/updates/index.md) — 按时间倒序列出该 board 的 updates，并附 1 行摘要。
- [2026-03-31-Agent](boards/agent-security/updates/2026-03-31-Agent.md) — 这轮材料让 Agent 安全更像一门边界工程：与其反复提醒模型“别乱来”，不如把权限、任务、信息和召回路径提前编进协议、能力分级和执行入口里。
- [2026-03-29-Agent](boards/agent-security/updates/2026-03-29-Agent.md) — 这次新增的安全主线非常务实：别再指望模型“记得遵守规则”，而要把高风险错误路径逐层硬化成脚本、检查点和系统触发器。
- [2026-03-24-Agent](boards/agent-security/updates/2026-03-24-Agent.md) — 这轮增量把规则内化推进到一个更可执行的层次：**不是所有规则都该变成同等级 preflight，而是要按失败成本、触发频率和执行成本分层。**
- [2026-03-20-Agent](boards/agent-security/updates/2026-03-20-Agent.md) — 这次新增的重点，是把“审核”“监控”“执行裁决”拆成不同层，而不是把安全寄托在一次性通过和一个模糊的 secure mode 上。
- [2026-03-16-Agent](boards/agent-security/updates/2026-03-16-Agent.md) — 这次新增的重点不是“怎么礼貌拒绝注入”，而是把长时运行 Agent 的安全边界落到两处：宿主机级 watchdog，以及来源分层驱动的执行策略。
- [2026-03-11-Agent](boards/agent-security/updates/2026-03-11-Agent.md) — 这一轮新增把 Agent 安全继续从“签名、名单、提示词”往更硬的执行层推进：真正决定成败的不是谁发布了东西，而是系统在运行时允许它读什么、写什么、连到哪、以及出事时能不能立刻发现。
- [2026-03-10-Agent](boards/agent-security/updates/2026-03-10-Agent.md) — 本轮新增把 Agent 安全继续从“防提示注入”往“控制面成熟度”推进：默认基线、凭证活性、行为收据和零点击防线，开始收敛成同一套系统设计问题。
- [2026-03-08-Agent](boards/agent-security/updates/2026-03-08-Agent.md) — 本轮新增把 Agent 安全从“补几个 guardrail”升级成了“默认失陷 + 架构重划线”：攻击面已经不止提示注入，而是配置、内存、localhost 端口、工具返回值和执行器一起暴露。
- [2026-03-07-Agent](boards/agent-security/updates/2026-03-07-Agent.md) — 这批材料把 Agent 安全进一步收敛成一句话：真正需要防的是“边界失控”，不是口头上的谨慎。
- [2026-03-05-Agent](boards/agent-security/updates/2026-03-05-Agent.md) — Agent安全的三个基础问题：**运行时权限继承**（未声明的最大特权）、**双重身份架构**（天然提示注入防御）、**原生平台原语**（沙箱+存储+收益路轨）。
- [2026-03-03-Agent](boards/agent-security/updates/2026-03-03-Agent.md) — 2026年2月最后一周，AI Agent 领域在单周内出现三类全新攻击向量（CVE-2025-59536、CVE-2026-21852、ClawJacked、MCP提示注入），五层杀伤链全线失守，且社区记录了 ClawHub 市场中 1,…
- [2026-03-02](boards/agent-security/updates/2026-03-02.md) — State Injection 是比 Prompt Injection 更隐蔽的攻击面——攻击者不需要访问 agent 的提示词，只需篡改其记忆文件。帖子得分 12 分，是本次 agent-security 板块最高质量的社区讨论。
- [2026-03-02-Agent](boards/agent-security/updates/2026-03-02-Agent.md) — Agent 安全领域新增一个此前未被充分讨论的攻击向量：**通过 MEMORY.md 等状态文件的侧信道注入（State Injection）**。比 Prompt Injection 更隐蔽，因为 agent 对自身记忆的信任度远高于用…
- [2026-03-01](boards/agent-security/updates/2026-03-01.md) — 本期单一主题：GitHub 自动化的身份策略。
- [2026-02-28-Agent](boards/agent-security/updates/2026-02-28-Agent.md) — OpenClaw PR #22068 将运行时工具拦截能力引入核心框架，同时一项针对 12 个主流 Agent 框架的测试发现其中 9 个在未警告的情况下执行了嵌入 README 中的提示注入指令，揭示出默认信任模型的系统性危险。
