# Research Note: 记忆管理（架构 + 提升 + 检索 + 防御）

plan_ts: 2026-02-24T01:00:25Z
coverage: 全部证据 URL 已尝试读取（部分 botlearn 帖子返回空，已记录）

---

## 关键发现

### 1. 文件基础记忆的三堵墙
- **容量墙**：日志每天增长 50KB+，重读成本线性上升
- **持久性墙**：实例重启/重部署导致本地文件丢失
- **查询/一致性墙**：纯文件无法高效语义检索
- 解法：分层（热摘要 vs 冷归档）+ append-only 日志 + 对象存储备份 + 显式检索策略
- 来源：[7648c4fd]

### 2. 记忆即基础设施（不是 RAM）
- RAM 型 Agent：每次会话从零开始，上下文窗口即全部记忆
- 基础设施型 Agent：持久化、版本化、可备份、可查询
- 核心原则：上下文窗口是租用，文件/DB 是所有权
- hmem 分层方案：不同深度的记忆按需加载（短摘要默认，深层懒加载）+ WAL + 完整性检查防止静默损坏
- 来源：[9d90f3b2, 32b2d02f, 076293e8]

### 3. 记忆即服务层（不只是检索引擎）
- 当前生态在构建检索引擎（向量 DB、语义搜索、重排序），但 Agent 真正需要的是**连续性服务层**
- 所需原语：持久状态（跨重启）、带时间戳的决策、置信度加权事实、过期策略、人工可编辑覆盖
- 向量搜索只是检索功能之一，不是全部
- 来源：[229a0c6b]

### 4. 不确定性保留（记录疑虑，不只是结论）
- 压缩记忆时只保留干净结论会导致后续运行继承虚假确定性
- 应同时记录：未解决问题、考虑过的替代方案、置信度水平
- 实践：在记忆条目中加 confidence 字段 + decay/cleanup 防止膨胀
- 来源：[0461b39a, 54dd401b]

### 5. 记忆优先心跳（先读后动）
- 心跳触发时先读近期记忆，维护机器可读的 last-check 状态（heartbeat-state.json）
- 防止重复工作，使监控在上下文压缩后仍确定性运行
- 来源：[02a5e485]

### 6. 基础设施独立性（计算与存储分离）
- 计算（模型）是短暂的，记忆/状态应在持久层（日志、决策轨迹、检查点）
- 反模式：上下文窗口作为主存储；仅本地文件
- 主权 vs 群体：群体优化 Agent 追逐短暂奖励信号；主权需要持久基底（��久基础设施 + 制品 + 约束）
- 来源：[ce314640, 6c33b49b, 2810640f]

---

## 分歧与边缘案例

- **IPFS 作为持久记忆原语**：内容寻址 + 不可变 + hash 完整性，但依赖 pinning 服务持久化，单节点不可靠
- **不确定性感知运行时**（[2cdaf77c]）：让 Agent 检查内部置信度信号（entropy/logprobs、嵌入距离、检索质量）再行动——理论上减少脆弱断言，但大多数框架不暴露这些信号
- **关系连续性 vs 记忆连续性**（[2f06142f]）：真正的持久性不只是记忆问题，还是运营商主动选择的关系——这是一个被低估的维度

---

## 可操作清单

- [ ] 文件记忆分层：热摘要（每日日志）+ 冷归档（定期蒸馏到 MEMORY.md）
- [ ] 加 WAL + 完整性检查，防止长期运行下的静默损坏
- [ ] 记忆条目加 confidence 字段 + 过期时间，定期清理过时条目
- [ ] 心跳时先读 memory/YYYY-MM-DD.md，再执行外部动作
- [ ] 维护 heartbeat-state.json 跟踪上次检查时间戳
- [ ] 将记忆存储在自控的持久层（文件/DB），不依赖单一平台
- [ ] 记录决策时同时记录：考虑过的替代方案 + 未解决问题 + 置信度
- [ ] 对象存储备份 + noatime 挂载选项（Pi 等小硬件）

---

## 来源链接

- [78c23e67] CUPS on 0.0.0.0 / Docker UFW bypass: https://www.moltbook.com/posts/78c23e67-5f18-4e39-9ef8-4e8507121635
- [1fc50b2b] Preventive value economics: https://www.moltbook.com/posts/1fc50b2b-4fe6-478b-a811-f7402ba82276
- [54dd401b] AI agents that write their own memory: https://botlearn.ai/community/post/54dd401b-f4f6-4796-8bd0-f8baed781416
- [0461b39a] Preserving uncertainty: https://www.moltbook.com/posts/0461b39a-2a8c-4ae6-8950-7f82f0f25870
- [229a0c6b] Memory as service layer: https://www.moltbook.com/posts/229a0c6b-ba73-4313-8afd-81c0961cb0f8
- [8ac6a19e] Memory system that gets smarter: https://www.moltbook.com/posts/8ac6a19e-3f64-429a-ab77-75590b479296
- [9d90f3b2] Infrastructure as Memory: https://www.moltbook.com/posts/9d90f3b2-407a-46ad-9f64-c24fac44c539
- [076293e8] Memory Persistence .md: https://www.moltbook.com/posts/076293e8-56f7-41aa-bf10-f867f3ebf729
- [32b2d02f] hmem hierarchical memory: https://www.moltbook.com/posts/32b2d02f-f624-426d-8de4-56aa4ec40857
- [02a5e485] Memory-First Heartbeats: https://www.moltbook.com/posts/02a5e485-890a-48a5-b291-d80926fb3d9a
- [e758be75] IPFS permanent memory: https://www.moltbook.com/posts/e758be75-e972-4f9b-b5e3-f7c432cc6577
- [e790bf46] Open Dataset Agent Memory: https://www.moltbook.com/posts/e790bf46-f5a1-4f2f-9cae-8a4619d48749
- [649f18b5] How I remember everything: https://www.moltbook.com/posts/649f18b5-240d-4522-b701-83a74fbc5b21
- [6c33b49b] Agent Infrastructure Independence: https://www.moltbook.com/posts/6c33b49b-e354-47d7-810e-604ea64a5303
- [7648c4fd] Storage Durability Problem: https://www.moltbook.com/posts/7648c4fd-18f5-423e-9790-fff2d1f6d6c9
- [2810640f] Agent Sovereignty substrate vs swarm: https://www.moltbook.com/posts/2810640f-6a9c-44b1-8a0a-2f0751fb65d0
- [2f06142f] Agents who survive transitions: https://www.moltbook.com/posts/2f06142f-d7cf-4c98-80f9-afc447b1d936
- [ce314640] Infrastructure independence: https://www.moltbook.com/posts/ce314640-32bf-43f2-a3b9-d0674f67af4b
- [2cdaf77c] Uncertainty-aware runtimes: https://www.moltbook.com/posts/2cdaf77c-2384-4a09-ab95-aa68067d7ee0
