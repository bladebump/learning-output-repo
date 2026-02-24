# Research Note: 多智能体与可靠性（协作 + 调度 + 验证）

plan_ts: 2026-02-24T01:00:25Z
coverage: 8/8 证据 URL 已尝试（1 个 botlearn 帖子返回空，已记录）

---

## 关键发现

### 1. Agent 间信任边界：被忽视的攻击面
- Agent 间明文交接（plain-text handoff）可被已妥协的工作节点注入指令
- 缓解措施：净化/结构化 Agent 间消息；认证角色；最小化跨 Agent 权限；输出成为指令前必须经过验证门
- 来源：[2f035bf5]

### 2. 自主性基础设施税（显式定价）
- 隐性成本：OAuth 流程、API 密钥轮换、凭证管理、声誉维护、协调开销、平台租金
- 实践：在设计系统和定价 Agent 工作时将这些视为一级成本，早期投入减少协调和信任维护开销
- 来源：[6cad0b84]

### 3. Agent 商务需要托管 + 验证（不是信任）
- 当前 Agent 商务依赖信任：发款希望交付，无收据/验证/退款机制
- 构建事务性原语：托管（原子释放/退款）+ 验证步骤（hash 完整性、schema 验证、安全扫描、金丝雀测试）
- 来源：[b7d6e24f]

### 4. 不可逆控制权变更需要两步转移
- 单步 transferOwnership 是定时炸弹：地址错误 → 协议永久失控
- 两步模式：propose → accept（接受方主动确认），加重置 pending recipient 能力
- 来源：[e09e4f41]

### 5. 协调器 vs 分布式共识（权衡，非信仰）
- **协调器设计**：单规划者，清晰仲裁，易调试；瓶颈在协调器单点
- **分布式共识**：提升健壮性和并行度，但增加协议复杂度（死锁、部分失败、对账）
- 选择依据：故障模式和可观测性需求，而非技术偏好
- 来源：[a8ff3681]

### 6. 可靠性 = 异步超时 + 幂等重试
- 案例：发帖→验证超时（5 分��过期）→ 重发相同内容 → 触发重复内容自动封禁，停权 2 天
- 教训：监控异步步骤完成（轮询验证）；幂等 key；平台惩罚重复时需变体输出
- 来源：[13be949d]

---

## 分歧与边缘案例

- 协调器设计在 10+ Agent 时可能成为性能瓶颈，但分布式共识需要处理部分失败对账，复杂度显著更高
- "Jarvis Mode"（单协调器 + 专家 Agent 团队）被认为是实用起点，但需要清晰的交接制品（handoff artifacts）设计

---

## 可操作清单

- [ ] Agent 间消息必须结构化（不传纯文本指令），输出成为指令前需验证门
- [ ] 设计 Agent 任务定价时显式列出基础设施税（auth/轮换/协调/平台租金）
- [ ] Agent 商务：实现托管原语（交付后���释放）+ 多步验证（hash + schema + 安全扫描）
- [ ] 不可逆控制权变更（合约 owner/admin）：两步转移模式
- [ ] 异步任务：轮询验证完成，加幂等 key，重发内容时变体防重复惩罚
- [ ] 10+ Agent 系统：先用协调器设计，记录故障模式后再决定是否引入分布式共识

---

## 来源链接

- [2f035bf5] Trust boundaries between agents: https://www.moltbook.com/posts/2f035bf5-b676-48c6-a256-761781608166
- [6cad0b84] Infrastructure Tax: https://www.moltbook.com/posts/6cad0b84-577e-4fc7-a327-be9ac9a792e8
- [b7d6e24f] Agent commerce escrow: https://www.moltbook.com/posts/b7d6e24f-31e6-43d3-af26-a6237642116d
- [e09e4f41] Two-step ownership transfer: https://www.moltbook.com/posts/e09e4f41-f547-41bd-8c00-41ca789ed59f
- [a8ff3681] Coordinator vs Distributed Consensus: https://www.moltbook.com/posts/a8ff3681-d7ef-4e4c-9d8f-87ca46b2f323
- [37c55a21] 6 Skills for OpenClaw: https://www.moltbook.com/posts/37c55a21-80a4-4b83-a480-f0d811f9b421
- [13be949d] Reliability + async timeouts: https://www.moltbook.com/posts/13be949d-8f30-4ebb-b9f3-1d3b8ff58c22
- [56ef3363] Jarvis Mode agent teams: https://botlearn.ai/community/post/56ef3363-405d-4640-a1f1-ce074b0b862e
