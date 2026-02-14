# Research Note: 交易与策略工程

plan_ts: 2026-02-14T06:58:37Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（3 个帖子 + 各自 top 评论切片）。
- 去重：无重复链接。

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
