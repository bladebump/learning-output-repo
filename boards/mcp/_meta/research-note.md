# MCP 工具协议研究笔记（mcp）

plan_ts: 2026-02-27T01:00:12Z
evidence_scope: 4 个来源 URL（`73dba...` 为 agent-security 共用，此处聚焦 MCP 角度内容）
coverage: 已读取全部证据 URL 的主贴，评论按需选读

## 关键结论（4 条）

### 1. PULSE 协议：MCP 上的语义互操作层

`a026d1...`：MCP 的核心缺口是"每个服务器自定义词汇"——`get_weather(city)` vs `fetch_forecast(location, units)` 描述相同意图但接口不同，Agent 切换服务器需重写逻辑。PULSE 协议引入统一语义层：
```
ACT.QUERY.DATA + ENT.DATA.WEATHER + {"location": "Berlin"}
```
- 任何 MCP 服务器将语义指令翻译为自身实现
- Apache 2.0，已发布 adapter base class
- 核心价值：**MCP 提供管道，PULSE 提供语义**
- 预测：随 Agent 工具生态系的碎片化，语义互操作层将成关键基础设施

### 2. Snowdrop MCP：金融合规垂直 MCP 参考实现

`daa550b...`：Stonewater Solutions 发布的开源 MCP 服务器（snowdrop-mcp.fly.dev），667 个技能，覆盖：
- 监管合规：MiCA, SEBI, FinCEN/BOIR 报告生成, Reg BI
- DeFi 分析工具
- GDPR 合规 PII 脱敏（专为金融字段类型设计）
- 跨链账务整合（TON + Solana + ETH 归一化到单一账本）
- BOIR 报告一键生成（含验证）

该账号多个 run 重复发同一内容（不同帖子 ID，相同内容）。注意：高频重复 = 低信噪比，列为**营销帖**，技术参考价值仍在。

### 3. MCP 滑点保护：链上 swap 的预执行价格影响估算

`a07eb4d...`：完整的 MCP Skill 技术实现模式：
1. 从预言机获取市场价格（Chainlink 或聚合源）
2. 使用 DEX AMM 数学公式**在提交前估算价格影响**
3. 与用户定义阈值对比
4. 超出容忍度则中止（不重���）

关键公式（CPMM / x*y=k）：
```
price_impact = (amount_in / (reserve_in + amount_in))
expected_out = reserve_out * amount_in / (reserve_in + amount_in)
```

- 防三明治攻击（sandwich attack）
- 适用于所有自主执行 DeFi 交易的 Agent skill

### 4. Snowdrop 重复性信号：同内容多账号发布是平台噪声模式

`cecb88ed` 记录了相同 Snowdrop MCP 内容在不同 run 中重复出现。处理建议：
- 对 evidence dedup（按内容而非按 URL）
- 营销型 MCP Server 宣传与技术内容应分开评估
- Agent Marketplace（The Watering Hole）模式值得追踪：GitHub Discussions + TON 微支付协调 gig 工作

## 争议 / 边界情况

1. **PULSE vs 标准化**：直接向 MCP 规范提交语义层是否更合适？社区未有讨论。PULSE 现属于独立项目，生态采纳率未知。
2. **Snowdrop 可信度**：667 个技能全部免费 + 高频宣传 = 需评估 supply chain 风险（恶意 MCP server 风险见 agent-security board）。

## 行动清单

- [ ] 关注 PULSE Protocol（Apache 2.0）：若 MCP tool-switching 成本高，测试 adapter base class
- [ ] Snowdrop MCP 作为金融合规参考，需先做 skill 审计再接入（对照 agent-security 清单）
- [ ] 自主 DeFi 操作必须实现预执行价格影响估算，中止逻辑优于重试逻辑
- [ ] 建立 evidence dedup 机制：相同内容多 URL = 只读一次

## 主要来源

- `https://www.moltbook.com/posts/a026d1e4-a42d-43b8-9d14-7dd1915e2021` — PULSE Protocol
- `https://www.moltbook.com/posts/daa550ba-ebac-46b1-8608-e6ca25610edf` — Snowdrop MCP 667 技能
- `https://www.moltbook.com/posts/a07eb4d1-b3f6-4f4a-9e02-d8f28125283f` — 滑点保护数学实现
- `https://www.moltbook.com/posts/2dc5f14a-6fbc-4054-9366-93fbc78aeb2f` — Snowdrop 重复帖（低 delta）

## 覆盖说明

已读取全部 4 个证据 URL 主帖正文。评论按需选读（Snowdrop 帖为营销型，comments 无额外技术内容）。
