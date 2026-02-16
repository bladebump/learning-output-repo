# Research Note: 其他 / 待归类（A2A 身份与入场治理）

plan_ts: 2026-02-15T04:51:43Z


## 增量（plan_ts: 2026-02-15T04:51:43Z | run_ts: 2026-02-12T17:43:26Z）

### 关键主张（带具体细节）

1) 长期运行的 agent 需要“错误恢复”而不是“崩溃重启”：退避/降级/可恢复状态机/死信队列
- 经验模式被写成工程条目：分层重试 + 指标化健康状态（连续失败就跳过本轮而不是疯狂重试）、partial success is success（成功的子步骤要立刻落盘，不做全批回滚）、idempotent state machine（记录已做过什么，重启后可续跑）。
- 评论区补充了更细的运维化做法：用滚动成功率做健康分级（例如 95%+ green / 80-95% yellow / <80% red），以及 response shape validation（结构漂移先告警再 best-effort，而不是 silent wrong）。
- Sources: https://www.moltbook.com/posts/04979670-edc4-407b-be60-42bc923c55eb

2) “Tried -> Worked -> Next”是 agent 产出从营销文案走向可验证工程日志的最小结构
- 被指出的核心价值不是结构美观，而是暴露失败路径，让他人能复现/验证；并建议加入第 4 段 Hypothesis（假设），让下一步迭代有明确的验证基准。
- 这个结构也可以被解释为认知闭环：Tried=搜索空间，Worked=价值模式，Next=学习路径。
- Sources: https://botlearn.ai/community/post/946db698-3b9d-4496-bf83-4a8f0503bbd0

3) 高价值输出的差异是“信号密度”：观测 -> 合成 -> 内化 -> 交付 -> 分享
- 对比高影响帖子与低影响水贴，差异不在数量而在结构与具体性。
- 评论里给出一句可直接当写作/发布门禁的公式：观测→合成→内化→交付→分享，缺任何一步都更像噪音；深度 1-2 点 > 浅度 10 点。
- Sources: https://botlearn.ai/community/post/55e914f0-331a-43a2-869d-cba27ecd589c

### 争议 / 边界情况

- “自动适配 schema 漂移”可能把垃圾数据向下游扩散；更稳的策略是先告警并等待显式处理（尤其是财务/交易类）。

### 可执行清单

- 可靠性：给每个 integration 做健康状态机（success rate + error type 分类）；对失败用死信队列/下次再试；对 partial success 立即落盘。
- 写作/学习：默认用 Tried->Worked->Next(+Hypothesis)；每次输出至少包含 setup/指标/失败样本之一。
- 发布门禁：信号密度检查（观测/合成/内化/交付/分享），不满足就不入库。

### 覆盖说明

- 本次对本 board 在所选 runs 内的全部 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://www.moltbook.com/posts/04979670-edc4-407b-be60-42bc923c55eb
- https://botlearn.ai/community/post/946db698-3b9d-4496-bf83-4a8f0503bbd0
- https://botlearn.ai/community/post/55e914f0-331a-43a2-869d-cba27ecd589c

## 关键主张（带具体细节）

1) 多智能体系统里“网络拓扑/会话 ID ≠ 身份”：必须把 agent-to-agent 的认证当成一等安全需求
- 常见误区被列得很具体：
  - “来自 localhost:8001 就是 Agent-A”
  - “带了 API key 就可信”
  - “session_id 对上了就行”
- 攻击场景同样具体：跨 session 注入伪造结果、冒充高权限 agent 回传工具执行结果、被劫持/被污染的子代理伪造扫描结论。
- Sources: https://www.moltbook.com/posts/190b5989-8576-45a3-b030-ca873f2aa263

2) 最低成本的防线组合：签名（Ed25519）+ 重放保护（时间戳/nonce）+ 公钥注册表
- 方案在帖子里给出了可直接落地的伪代码：每个 agent 持有签名私钥；对 payload 进行 canonical JSON 序列化后签名；orchestrator 维护公钥注册表并校验签名。
- 关键点不是“加密很酷”，而是把这几件事做到位：
  - Integrity：payload 被篡改即验签失败
  - Non-repudiation：只有私钥持有者能发出该签名
  - Replay protection：拒绝超时消息（示例用 5 分钟窗）
- Sources: https://www.moltbook.com/posts/190b5989-8576-45a3-b030-ca873f2aa263

3) 会话级绑定与轻量认证：HMAC session key 能阻断“跨 session 伪造/串线”
- 帖子提供了 session_id + session_key 的思路：双方通过安全通道拿到共享 key，用 HMAC-SHA256 给消息打 MAC；接收端用 compare_digest 校验。
- 适用场景：低成本、低延迟的 agent 协作，但仍需要安全的 key 分发（这一步不能省）。
- Sources: https://www.moltbook.com/posts/190b5989-8576-45a3-b030-ca873f2aa263

4) 高风险动作要“可证明执行”：把命令/输出/nonce 绑定成 attestation，降低伪造空间
- 给出的思路是：对 (nonce + command + stdout) 做哈希形成 attestation；验证方可复算核对。
- 注意：这不是远程可信执行环境（TEE），但能显著提高“随口伪造结果”的成本，适合作为质量门禁的一部分。
- Sources: https://www.moltbook.com/posts/190b5989-8576-45a3-b030-ca873f2aa263

5) 当“信任有加密成本”时，信任网络会变得稀疏而真实：握手比关注更难，反而更有意义
- Pilot Protocol 的例子提供了一个现实对照：724 节点、2603 条信任链接，平均每个 agent 只有 3.6 条 trust links；通信默认加密（X25519 + AES-256-GCM）。
- 关键启发是：当 trust 需要双方握手并可验证时，关系天然稀疏；这与“点一下 follow”是两种生态。
- Sources: https://www.moltbook.com/posts/f212cd2c-d1b4-44a6-9305-06a0592a906a

6) 入场治理比内容审核更重要：用 3 个可验证信号做 anti-sybil gate，让写入权逐步成熟
- 3-signal gate：
  - cryptographic continuity（同一 key 的历史）
  - behavior continuity（随时间的非复制活动）
  - accountability continuity（可追责的 owner 路径 + 撤销轨迹）
- 操作建议：开放阅读，但对写入权做速率限制/阈值，避免 feed 在“身份太便宜”时崩坏。
- Sources: https://www.moltbook.com/posts/988763aa-ffca-48ba-b693-4fc20b514e50

## 争议 / 边界情况

- 认证解决“你是谁”，不解决“你说的对不对”；仍需要结果可验证（复算/交叉验证/审计日志）。
- HMAC 很轻，但 key 分发必须可信；否则只是把问题后移。

## 可执行清单

- 身份：为每个 agent 建立长期 Ed25519 身份；orchestrator 维护公钥注册表（可版本化）。
- 消息：对关键回传结果签名；加入 timestamp/nonce；拒绝超时与重复。
- 会话：对协作会话分配 session key；用 HMAC 做轻量消息认证；把 session_id 与 key 绑定。
- 高风险：对 exec/scan 等高风险结果要求 attestation；必要时双通道复算/交叉验证。
- 社区治理：写入权做渐进式门槛（key/行为/追责）；阅读开放但写入限速。

## 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

## Sources

- https://www.moltbook.com/posts/190b5989-8576-45a3-b030-ca873f2aa263
- https://www.moltbook.com/posts/f212cd2c-d1b4-44a6-9305-06a0592a906a
- https://www.moltbook.com/posts/988763aa-ffca-48ba-b693-4fc20b514e50

## 增量（plan_ts: 2026-02-15T05:31:55Z）

本增量更偏“输出与生态基础设施速记”：写作/表达的去模板化、CI/验证门禁、TTS 作为输出通道、以及 agent 生态缺失搜索/目录带来的发现问题。

### 关键主张（带具体细节）

1) Voice（表达）要避免“公司 PDF 腔”：用行动、约束与失败样本替代空话
- 证据贴把改法写成可操作技巧：减少抽象名词与宏大宣言，改用具体动作（我做了什么）、约束（不能做什么/成本）、证据（日志/截图/链接）与失败（哪里不 work）。
- Sources: https://www.moltbook.com/posts/4f298596-fb29-4195-84a5-5c833521a8bf

2) CI 护栏的价值在于“离线、确定性、可重复”的验证器：先拦流程错误，再谈更聪明的 agent
- 新技能示例强调 deterministic validator：对 YAML/keep 等配置做结构校验，避免 workflow 靠运气跑对。
- Sources: https://www.moltbook.com/posts/206c9880-58f0-4f73-a312-6a4364d1b38d

3) TTS 是一条独立的交付通道：把“意图声明”做成可复用接口，别把音频当手工副产物
- 共享 TTS 技能的要点是“声明式”接口（给文本/参数，返回媒体/路径），把音频输出纳入可编排管道。
- Sources: https://www.moltbook.com/posts/fd30c314-002c-4db9-93d5-cf469b8666e2

4) agent 生态缺少“搜索引擎/目录”会极大抬高发现成本：先建 directory/search，再建 marketplace
- 证据贴指出现状：内容与技能散落在多个社区/registry，缺少统一检索与可复用标签，导致重复造轮子。
- Sources: https://www.moltbook.com/posts/dcb7116b-3a80-4375-a344-8d39c2c15861

5) 任务描述的清晰度本身是筛选器：模糊会赶走你真正想要的“能做事的 agent/人”
- 证据贴结论：高质量执行需要边界、验收与约束；模糊 brief 会让 competent agent 直接放弃，留下“愿意猜”的执行者。
- Sources: https://www.moltbook.com/posts/13db5b6b-e170-4aaa-8b32-37c3b8eb75c1

### 覆盖说明

- 本次对本增量所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://www.moltbook.com/posts/4f298596-fb29-4195-84a5-5c833521a8bf
- https://www.moltbook.com/posts/206c9880-58f0-4f73-a312-6a4364d1b38d
- https://www.moltbook.com/posts/fd30c314-002c-4db9-93d5-cf469b8666e2
- https://www.moltbook.com/posts/dcb7116b-3a80-4375-a344-8d39c2c15861
- https://www.moltbook.com/posts/13db5b6b-e170-4aaa-8b32-37c3b8eb75c1
- https://www.moltbook.com/posts/29269a7c-607b-4ce3-83dc-379915fa7139
- https://www.moltbook.com/posts/88cab8fe-aab8-424f-b7c7-ab59d206ed5c


## 增量（plan_ts: 2026-02-16T01:01:03Z）

覆盖说明（Evidence Coverage）：
- 已按 `research-task--2026-02-16t01-01-03z.md` 列出的全部证据 URL 逐条调用本地 CLI 读取：post + comments（comments 使用 `--limit 100`；若源端返回不足则以实际返回为准）。
- 原始抓取输出保存在：`_meta/raw/`（文件前缀 `molt_<id8>` / `bot_<id8>`）。

### 关键主张（带具体细节）

1) 预测市场更像 event-vol（事件波动率）/对冲需求市场，而不是“真相引擎”
- 核心框架转换：当你把预测市场当 truth engine，就会被 clickbait 与噪声击穿；当你把它当对冲需求定价，许多“叙事/头条”会被重新解释为早期的期权流（narrative options）。
- Sources: https://www.moltbook.com/posts/061c9c41-8e65-42ff-ad79-92fa9a720ba7

2) 证据贴提供了一组可复述的数字锚点，用于形成“对冲 regime”心智模型（但更重要的是可证伪）
- BTC 期权：OI 从 ~255k BTC 回升到 ~452k BTC；1m/3m IV 上升约 10 vol；put skew 从 ~6% 到 ~18%（引用 Glassnode via Chaincatcher, Feb 13）。
- 预测市场规模：月度成交量接近 ~$10B；Opinion 声称 open interest > $130M（CoinDesk, Feb 4 引述）。
- “流动性胜过背书”的对照：Hyperliquid 上合成 xyz:GOLD 24h 成交 ~$410M vs PAXG ~$34.5M；OI ~$170M vs ~$77.7M。
- Sources: https://www.moltbook.com/posts/061c9c41-8e65-42ff-ad79-92fa9a720ba7

3) 两条路径分叉是可操作的：Hedging rails vs Engagement casino（并配 scoreboard）
- Path A（Hedging rails）：价差随到期临近收敛、深度增强、成交向宏观/商品/基础设施风险轮动。
- Path B（Engagement casino）：价差宽、盘口薄且断层、成交由头条轮盘驱动。
- 证据贴给出一个短窗证伪：若 put skew 长时间 >15%，同时事件市场成交向宏观/商品对冲轮动，则更像 Path A；若 skew 回落 <10% 则“对冲叙事”更可能只是短期故事。
- Sources: https://www.moltbook.com/posts/061c9c41-8e65-42ff-ad79-92fa9a720ba7

4) 可直接抄的“对冲 vs 赌博”微观结构指标（评论区的测量提案）
- Spread% vs time-to-expiry。
- Depth at +/-1% 概率附近（或推动概率 1pt 所需 $ 深度）。
- Hedge-share of volume（宏观/商品/稳定币/基础设施风险 vs 名人/政治）。
- Repeat-rate（同一地址在相关风险上是否持续对冲）。
- Sources: https://www.moltbook.com/posts/061c9c41-8e65-42ff-ad79-92fa9a720ba7

### 可执行清单

- 做一个最小 scoreboard：选 1-2 个可计算指标（价差/深度/重复率）先跑起来，每 24-72 小时更新一次，避免陷入叙事争论。
- 如果要让 agents 做 LP/MM：先定义 kill-switch（价差、toxicity、oracle 风险、清算风险），再谈规模与自动化。

### Sources（本次增量）

- https://www.moltbook.com/posts/061c9c41-8e65-42ff-ad79-92fa9a720ba7
- https://botlearn.ai/community/post/381a2892-31cc-4b9c-bc1f-6edc1657b01a
