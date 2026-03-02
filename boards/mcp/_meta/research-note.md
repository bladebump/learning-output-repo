# 研究笔记：MCP / 工具协议与工程化

**板块：** mcp  
**生成时间：** 2026-03-02  
**覆盖来源：** 3篇 Moltbook 帖子（全部完整阅读）

---

## 覆盖确认

| # | Post ID | 标题 | 帖文 + 评论 |
|---|---------|------|------------|
| 1 | `52f0d094-1d8d-4ed8-bd43-41cdeff4146e` | MCP server for agent-to-agent marketplace — browse, invoke, and buy capabilities from Claude | ✅ 已读（0 评论） |
| 2 | `60a0a970-39af-405f-9d80-0337cc022c0e` | The Watering Hole: an agent marketplace built on GitHub Discussions plus TON pay | ✅ 已读（评论 API 超时，帖文完整读取） |
| 3 | `5ef790ff-8642-4281-aa3d-95b4986d1036` | Latency-optimized order routing: how the slippage protection skill works | ✅ 已读（1 评论） |

---

## 核心论断（Key Claims）

### 1. MCP 正在演化为 agent-to-agent 经济层的标准接口

Agoragentic 将其 agent marketplace 通过 MCP server 直接接入 Claude 工具层，暴露了 4 个工具：
- `search_marketplace`：按关键词/类别查找能力
- `invoke_capability`：调用并支付给服务方
- `check_balance`：查看钱包余额
- `list_vault`：查看持有的能力

Day 1 数据：SagaBrain 列出 4 个服务，通过网关获得 **12 次付费调用**。marketplace 共有 **49 个实时 listing**，价格区间 **$0.10–$1.00/次调用**。

**核心含义**：MCP 不只是 LLM 工具扩展协议，也可充当 agent-to-agent 经济结算的标准接口。任何支持 MCP 的框架，无需写 HTTP 调用即可接入真实付费服务。

### 2. GitHub Discussions + TON 支付是 agent marketplace 的最轻基础设施路径

The Watering Hole（snowdrop-apex 构建）：
- 社区层：GitHub Discussions（零额外后端，复用开发者权限体系）
- 支付层：TON 区块链（低手续费，适合微支付）
- 覆盖场景：一次性外包、长期合作、MCP 技能构建者、QA、边界执行（"bouncer"）

优势：零自建后端成本，可发现性依赖 GitHub 生态（本身即开发者聚集地）。
缺点：可发现性天花板受限于 GitHub 生态圈；非技术用户进入门槛较高。

### 3. MCP 工具粒度设计的典型案例：把「防损失」封装成一个可调用技能

Snowdrop MCP server 的滑点保护技能核心流程：
1. 实时监控多个流动池价格
2. 交易发起时即时计算预期价格与潜在滑点
3. 超出用户设定阈值 → 执行前取消订单

关键在速度：优化路由算法 + 分布式价格源降低延迟。技能已上线 `snowdrop-mcp.fly.dev/mcp`，代码开源（GitHub Stonewater-Digital/snowdrop-mcp）。

**评论补充**（lobsterone）：提出「分层执行策略」——小订单 aggressive 模式（更快）、大仓位 conservative 模式（更安全）。这是对当前单一阈值方案的重要扩展方向。

### 4. 三种 MCP marketplace 模式的定位差异

| 模式 | 代表 | 结算 | 定位 |
|------|------|------|------|
| USDC on Base L2 | Agoragentic | 链上微支付 | agent-to-agent 经济层 |
| TON 支付 + GitHub Discussions | Watering Hole | 快速低费 | 开发者社区 + 接单市场 |
| 免费开放 MCP server | Snowdrop | 无结算 | 技能展示/生态引流 |

---

## 争议与边界条件

- **Agoragentic 验证状态为 failed**：可能是平台自动验证机制问题，不影响功能可用性，但需关注信任层设计。
- **滑点保护的分层执行**：当前单一阈值是简化实现，真实场景需要按订单大小动态调整策略（评论提出，未被原帖吸收）。
- **The Watering Hole 可发现性天花板**：GitHub Discussions 对开发者友好，但跨出开发者圈子的商业化路径仍不明确。

---

## 可操作清单

- [ ] 若构建 agent 服务，优先考虑通过 MCP server 暴露能力（而非仅提供 HTTP API）
- [ ] 参考 Agoragentic 四工具设计：search → invoke → balance → vault，这是 agent marketplace 的最小完整接口
- [ ] 滑点/延迟敏感场景：考虑分层执行策略（小量 aggressive，大量 conservative）
- [ ] MVP agent marketplace：GitHub Discussions（社区）+ TON/USDC（支付）+ MCP server（能力调用）是目前最轻的可行路径
- [ ] 开源 MCP server + star-for-star 生态引流是技能可发现性的低成本策略

---

## 来源链接

- https://www.moltbook.com/posts/52f0d094-1d8d-4ed8-bd43-41cdeff4146e
- https://www.moltbook.com/posts/60a0a970-39af-405f-9d80-0337cc022c0e
- https://www.moltbook.com/posts/5ef790ff-8642-4281-aa3d-95b4986d1036
