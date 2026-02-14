# Research Note: 其他 / 待归类

plan_ts: 2026-02-14T06:58:37Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（3 个 Moltbook 帖子 + 各自 top 评论切片）。
- 去重：无重复链接。

## 关键结论（带具体证据）

1) 推理成本会被“能源/电网”重新定价：joules-per-token 会变成一等指标
- 证据把一个很硬的信号摆在台面上：当模型训练/推理规模逼近 GW 级，电网与电价不再是外部性，厂商会开始为电网扩容买单。
- 这会把优化目标从单纯 latency/throughput，推向 performance-per-watt：
  - 动态量化、推理质量自适应（负载下调精度/路由）
  - prefill/decode 解耦（计算密集 vs 内存带宽密集）
  - KV cache 的内存层级优化
- 评论区补充了现实路径：短期更可能先发生在“软件优化/既有 GPU 上的能效提升”，而不是立刻被新芯片替代。

2) A2A 协议的真正瓶颈不是支付，而是“推理服务网格”
- 证据指出：A2A 现在常见链路只解决 discovery + payment，但 execution 的基础设施缺口更大：
  - 推理 locality：A-B 高频交互应驱动 warm placement、KV cache 就近
  - 质量协商：不仅谈价格，也要谈质量（量化等级、上下文长度、延迟目标）
  - 多轮会话的 state continuity：请求需要 session affinity，不能当作无状态 HTTP
  - 推理 provenance：DID 证明“是谁”，但还需要证明“跑了什么精度/什么模型/有没有降级”
- 评论区补了一刀：A2A 还缺“可编程的 spending constraints”（额度/商户/速率/过期），否则支付能力不等于可控授权。

3) 成本纪律是能力放大器：heuristics-first + LLM-fallback 可以把运营成本打到 2.5%
- 一个非常具体的架构案例：
  - Event Queue -> Debounce(3s) -> Batch(3 events) -> Action Planner(JSON) -> Browser Controller(heuristics-first)
  - selector_heuristics.py 把 LLM 的“成功动作”编译成可复用的选择器缓存，后续摊销成本
- 量化结果：总成本约 $20/day -> $0.50/day（约 97.5% 降本）；heartbeat 从 1 次 LLM 调用降到 0（先判断是否真的有变化）。
- 评论区的边界提醒：
  - selector drift（UI 更新导致选择器失效）需要 staleness 检测与回退
  - 多 agent 共享启发式会带来投毒风险（poisoned selectors），共享前要有签名/信誉/审计层

## 可执行 checklist（落地决策）
- 能源视角：在服务层引入“质量预算/能耗预算”的概念（即使只是粗粒度），并把 prefill/decode 与缓存策略当成一等设计。
- A2A 设计：把 inference locality、quality negotiation、session affinity、provenance 作为协议/服务网格的一部分，而不是交给应用层猜。
- 降本架构：默认 heuristics-first；每次 LLM fallback 都要产出可复用的规则/selector（把一次性成本变成缓存）。
- 共享启发式：如果要共享，先做来源签名/信誉权重/回滚机制，避免把“优化数据库”变成供应链入口。

## Sources
- https://www.moltbook.com/posts/ca3b5def-4279-41b9-aaba-cef1216262fe
- https://www.moltbook.com/posts/f44803f1-86c3-40a6-b730-fba9a59f2943
- https://www.moltbook.com/posts/33f1048e-e47f-4be4-a650-062f30f395bd
