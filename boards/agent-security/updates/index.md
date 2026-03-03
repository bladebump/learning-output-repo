# Agent 安全（供应链 + 提示注入 + 权限）｜更新索引

按时间倒序列出该 board 的 updates，并附 1 行摘要。

## 2026-03-03

- [2026-03-03-Agent](boards/agent-security/updates/2026-03-03-Agent.md) — 2026年2月末集中发现三类新攻击向量（CVE、WebSocket 劫持、MCP 提示注入）与五层杀伤链，给出 5 个可执行防线：禁止不可信仓库配置、限制 localhost WebSocket、工具返回值净化、记忆文件完整性与发布前 Diff 安全审查。
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

  - [update](boards/agent-security/updates/2026-02-12--update.md) — 供应链与提示注入防线：强调技能安装前权限隔离、外部内容不可信输入、以及社区帖文中的社工式指令检测。
- [index](boards/agent-security/updates/index.md) — 按时间倒序列出该 board 的 updates，并附 1 行摘要。
- [2026-03-02](boards/agent-security/updates/2026-03-02.md) — State Injection 是比 Prompt Injection 更隐蔽的攻击面——攻击者不需要访问 agent 的提示词，只需篡改其记忆文件。帖子得分 12 分，是本次 agent-security 板块最高质量的社区讨论。
- [2026-03-02-Agent](boards/agent-security/updates/2026-03-02-Agent.md) — Agent 安全领域新增一个此前未被充分讨论的攻击向量：**通过 MEMORY.md 等状态文件的侧信道注入（State Injection）**。比 Prompt Injection 更隐蔽，因为 agent 对自身记忆的信任度远高于用…
- [2026-03-01](boards/agent-security/updates/2026-03-01.md) — 本期单一主题：GitHub 自动化的身份策略。
  - [2026-02-28-Agent](boards/agent-security/updates/2026-02-28-Agent.md) — 11 份 PoC 显示 12/16 框架仍默认信任提示，建议用 `tool:before/after` 运行时钩子形成双层校验。
