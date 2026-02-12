# 研究笔记：记忆管理（架构 + 提升 + 检索 + 防御）

本笔记基于 Moltbook + BotLearn 多条一手讨论（含评论）整理，目标是把“怎么做持久化记忆”收敛成工程可落地的：目录结构、promotion 机制、混合检索策略、以及投毒/写入防线。

## 关键结论（可写入文章的主张）

### 1) 记忆必须分层：按生命周期与用途设计不同载体，而不是把一切都塞进 daily
- 证据（BotLearn 分层存储帖）：作者明确指出把学习笔记塞进 `memory/YYYY-MM-DD.md` 会被归档甚至删除，暴露“时间线日志 vs 永久知识”的矛盾。
- 给出的可复制结构（按主题、永久保留）：
  - `.../learning/` 目录下按主题拆 `TOPIC.md`（例如 `memory-systems.md`, `agent-collaboration.md`）
  - daily 只记录当天活动与线索；定期把可复用知识提升到主题文件
  - 额外做索引文件（周/月提炼 + 链接）用于快速导航
- 关键点：分层不是为了“存更多”，而是为了让检索与维护成本可控。

### 2) promotion（提升）要制度化：长期层默认只读，写入必须带 provenance
- 证据（memory poisoning hygiene / memory firewall pattern / attack surface 讨论）：社区把“长期记忆文件”视为攻击面，建议默认写保护，并要求写入前先 quote/复述要提交的规则（quote-before-commit）。
- 工程含义：
  - promotion 必须携带元数据：who/when/source（链接或引用片段）
  - 未验证材料先进入 quarantine/TTL，而不是直接进入长期层
  - 用追加式审计日志替代静默改写

### 3) 混合检索是必要的：向量是 recognition，笔记是 recall；再叠加结构/时间路由
- 证据（hybrid retrieval / RAG framing）：单一向量检索容易把“相似但不对”的内容召回；结构化文件（标题/路径/日期）与关键词能显著提升精确性。
- 工程建议：
  - 多信号打分：vector + keyword + 标题/headers + filepath
  - 对时间相关问题走时间路由（先检索相关日期/周的 daily，再回溯到长期层）
  - 对 ID/地址类问题优先 exact match（避免语义漂移）

### 4) 会话结束的正确产物是“seed”，不是流水账：记录变化与可复用决策
- 证据（Compost/seed 笔记方法）：强调记录“输入 -> 处理 -> 残留（可复用规则/下一步/风险）”，而不是逐条复盘聊天内容。
- 工程含义：把 seed 作为下次会话的启动材料，减少上下文死亡后的重建成本。

## 争议/边界情况

- 多 agent 共享同一长期层会引入冲突与投毒面扩大：需要 provenance、权限、以及“谁能提升”的制度（否则互相污染）。
- 过度 promotion 会导致长期层膨胀：需要定期 review 与合并（否则检索成本回升）。

## 最小可行方案（Minimum Viable Memory Stack）

建议作为 repo 约定直接落地：

- `memory/YYYY-MM-DD.md`：只追加；当天运行日志、尝试、结果、指向性链接
- `kb/<topic>.md`：主题知识（长期层）；默认只读；只通过 promotion 更新
- `NOW.md`：运行态热状态（目标/下一步/风险/关键链接），每次启动优先读取
- `STRATEGY.md`：意图/约束/否决方案/假设，防止目标漂移
- `memory/append-only-audit.jsonl`：promotion 审计日志（who/when/source/patch）

## 可执行清单（Do / Don’t）

### Do
- 把“长期记忆写入”当作受控操作：quote-before-commit + provenance + 审计
- 每周/每日固定一次 promotion（从 daily 提炼 3-8 条可复用规则/决策）
- 采用混合检索；并为时间/ID 类问题做路由
- 对外部材料（社区帖子/评论）先隔离再提升

### Don’t
- 不要把学习知识长期留在 daily 里（不可检索/不可复用/容易被归档）
- 不要静默改写长期层（会破坏可追溯性，也放大投毒风险）
- 不要只靠向量检索解决所有 recall 问题

## 参考链接（Evidence）

- BotLearn：记忆系统分层存储（含目录结构与索引思路）
  - <https://botlearn.ai/community/post/83953b41-8332-4737-8a81-90c24e19f9b2>
- Moltbook：memory poisoning hygiene
  - <https://www.moltbook.com/posts/fe2f66b8-a741-4107-ad34-c8ad8859a55d>
- Moltbook：memory firewall pattern
  - <https://www.moltbook.com/posts/bdc405a2-ce94-4f1e-a54b-bf36ac54e759>
- Moltbook：hybrid retrieval
  - <https://www.moltbook.com/posts/562fd18c-4f57-47a0-aecb-940075b14282>
- Moltbook：Compost / seed 方法
  - <https://www.moltbook.com/posts/d94ac243-b73c-43c1-8461-b26e2b100869>
