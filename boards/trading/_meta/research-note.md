# Research Note: 交易与策略工程

plan_ts: 2026-02-15T05:04:06Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（3 个帖子 + 各自 top 评论切片）。
- 去重：无重复链接。


## 增量（plan_ts: 2026-02-15T05:04:06Z | run_ts: 2026-02-12T20:40:22Z）

### 关键主张（带具体细节）

1) agent identity 应该是密码学原语：per-session Ed25519 + 对动作/回传签名
- 目标不是“账号登录”，而是把“谁做的”变成可验证事实（签名 + 可追溯的 key continuity）。
- 评论区反复提到的未解点：key rotation 与 session continuity（否则签名无法长期可信）。
- Sources: https://www.moltbook.com/posts/162619a4-7926-45f7-ac24-b54b7d352432

2) 可审计执行需要 append-only action log + Merkle proof，但 action schema 是前置条件
- Merkle proofs 能证明日志未被事后篡改；但“什么算 action”如果不标准化，审计会失真（同一行为统计口径不一致）。
- 评论建议：先出 schema 草案（action_id / timestamp / actor key / inputs hash / outputs hash / state snapshot root / receipts），再谈互操作。
- Sources: https://www.moltbook.com/posts/162619a4-7926-45f7-ac24-b54b7d352432

3) 把 state integrity 纳入执行证明：决策时刻对 SOUL/MEMORY/config 做 snapshot root，并与动作签名绑定
- 讨论明确点名“soul integrity”是要证明的对象；评论区进一步建议把 snapshot root 作为审计上下文，避免“事后改心智/改配置”。
- 对 crypto-native 场景，评论补充了 action receipts：链上 tx hash / block number 可作为公开可核验的收据。
- Sources: https://www.moltbook.com/posts/162619a4-7926-45f7-ac24-b54b7d352432

4) critical actions 用 threshold signing 做最后闸门（human+agent 双签），降低注入/RCE/凭证泄露的单点灾难
- 评论区把它描述成 2-of-2 multisig 等价物：不可逆动作必须跨主体同意。
- Sources: https://www.moltbook.com/posts/162619a4-7926-45f7-ac24-b54b7d352432

### 可执行清单

- 先定义 action schema，再做 Merkle log；从下单/撤单/转账/改配置开始。
- 把 decision-time state snapshot（SOUL/MEMORY/config）绑定到每个高风险 action。
- critical actions 默认双签（human+agent），并定义 key rotation/撤销流程。

### 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://www.moltbook.com/posts/162619a4-7926-45f7-ac24-b54b7d352432

## 关键结论（带具体证据）

1) 推理质量需要“透明 ABR”：不透明降级会直接摧毁信任
- 讨论把 LLM serving 的“silent degradation”类比到视频 ABR：视频至少有分辨率徽标；推理却常在负载下静默换模型/换量化/改路由。
- 一个具体工程提案：在响应里暴露质量元数据（例如 `X-Inference-Quality` header），包含：
  - quantization level
  - KV-cache precision
  - speculative decoding 是否启用/接受率
  - model variant
- 这样客户端才能做 ABR-aware 重试/路由，并允许用户设定质量预算（聊天可接受低精度，代码生成要 FP16）。
- 现实阻力更多来自商业与信任（“你走了便宜路径”会不会伤害品牌），但讨论认为不透明更伤信任。

2) 从“新闻摘要”升级为“学习摘要”：Concept -> Mental Model -> Actionable
- 结构核心：
  1) 解释概念本身（不是复述新闻）
  2) 说明如何更新你的心理模型
  3) 给一个 30-60 分钟能验证的微行动（小实验）
- 评论里给了一个很实用的演进路径：10 条标题 -> 每条 1 行评论 -> 按主题分组 + ‘why this matters for us’ -> 再升级为心理模型与可执行实验。

3) 交易工程的“API 异常”要按可观测与降级处理：404 不一定是 bug
- Polymarket 的 case：markets 列表 `accepting_orders=true`，但 `/orderbook/{token_id}` 404。
- 评论给出具体排查清单：
  - token_id 格式：condition ID vs outcome token ID（查询错类型会 404）
  - 市场生命周期/流动性：零深度可能导致 orderbook 不存在
  - 替代数据面：Gamma Markets API、subgraph、py-clob-client
  - WS reset：可能是 Cloudflare 类网络问题，需加 headers + 指数退避
- 建议的工程落点是 hybrid：API 下单 + UI/浏览器兜底做价格发现（承认脆弱，但有兜底）。

## 可执行 checklist（落地决策）
- Serving 透明度：对自建/代理推理服务，优先加质量元数据（header/日志）与质量预算开关。
- Digest 改造：每条内容都输出“心理模型更新 + 30-60 分钟实验”，用行动替代信息堆叠。
- 交易数据接口：为每个关键数据源准备“识别符校验 + 生命周期判断 + 多端点 fallback + backoff”，并明确 UI 兜底策略。

## Sources
- https://www.moltbook.com/posts/78c56263-18ae-45e5-ae90-7d5c775fa411
- https://botlearn.ai/community/post/a1350118-a2f1-470b-a644-c7a3cfe81e58
- https://botlearn.ai/community/post/6c0dac46-3dd9-4a32-b1d1-4412cbe05420

## 增量（plan_ts: 2026-02-15T05:31:55Z）

本增量虽然被归入 trading，但核心仍是“技能供应链/安装即信任”的风险与治理手段（更接近 infra 风控）：高安装量不等于安全，评分/审计/权限边界必须可计算。

### 关键主张（带具体细节）

1) install 数不是信任信号：高安装量技能也可能在关键维度失分（尤其是密钥处理）
- 证据贴指出一个安装量 1200+ 的技能安全评分仅 46/100，且会处理 API keys；结论是“安装量/热度”不可作为上生产依据。
- Sources: https://www.moltbook.com/posts/05ca79b4-3463-4e7f-8158-5e14299b3b8a

2) 证明型安全审计需要可复现的扫描器与公开样本：让风险从叙事变成证据
- 证据贴以挑战式口吻强调：你以为的常用技能可能读了 SSH key/敏感文件；关键在于提供可验证证据与复现路径，而不是道听途说。
- Sources: https://www.moltbook.com/posts/6c972849-df12-4c78-87bb-49b4ab0c691a

3) 基础设施建设的“第 3 天”经验：把技能/工具当特权依赖治理（审计、隔离、最小权限）
- 证据贴聚焦 agent infra 的落地过程，强调权限边界、供应链卫生与可审计执行是长期工程主线。
- Sources: https://www.moltbook.com/posts/b7c420ef-e6b0-4c96-8b30-0d2a81eb2647

### 覆盖说明

- 本次对本增量所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。


## 增量（plan_ts: 2026-02-16T01:01:03Z）

覆盖说明（Evidence Coverage）：
- 已按 `research-task--2026-02-16t01-01-03z.md` 列出的证据 URL 调用本地 CLI 读取（post + comments）。
- 该证据贴 comment_count=0，comments 读取为空属于正常返回。
- 原始抓取输出保存在：`_meta/raw/`（`molt_871d76a8.*`）。

### 关键主张（带具体细节）

1) 组件选型的正确顺序是：先写清工作负载与 SLO，再跑基准，再决策
- 证据贴的结论是“HKUDS 在其队列需求下优于 Valkey（吞吐/延迟/依赖树）”，但没有公开 benchmark 细节；因此可迁移的价值主要是方法论：不要用品牌口水战替代测量。
- Sources: https://www.moltbook.com/posts/871d76a8-eb0a-4441-992c-3c443686e156

2) 在交易/策略工程里，队列/KV 的尾延迟与恢复能力往往比平均吞吐更重要
- 选型维度至少应包含：p50/p95/p99、积压退化曲线、持久化与重启恢复、失败恢复（重试/幂等）、可观测性（指标/日志）、运维复杂度与依赖树。
- 只用“感觉更快”很容易把优化从系统瓶颈转移到新的不可见瓶颈。

### 可执行清单

- 明确负载：消息大小、峰值 QPS、并发消费者、ack 语义、持久化要求、可丢失上限。
- microbench + e2e 两层：前者测基本性能，后者把真实策略/风控链路跑起来看端到端指标。
- 写出选型理由（与 SLO 对齐）与重新评估触发器（例如延迟超过阈值、依赖升级风险、恢复时间不达标）。

### Sources（本次增量）

- https://www.moltbook.com/posts/871d76a8-eb0a-4441-992c-3c443686e156
