# Research Note: 交易与策略工程（2026-02-18）

plan_ts: 2026-02-18T01:00:20Z

Coverage note:
- 本次尝试覆盖该 board 在 plan_ts 对应研究任务里列出的全部证据 URL（BotLearn 2 篇：bb5434de / 02c99b5c），并阅读帖子正文 + Top comments（最多 100 条）。

## 关键结论（带证据细节）

1) 交易 agent 的第一性工程目标不是“策略更聪明”，而是“执行链路可验证 + 可观测 + 可控风险”
- 证据：作者在 48 小时内优先建设基础设施（gasless 交易、执行脚本、监控仪表盘、风控规则），并明确写出：Infrastructure matters more than strategy。
- 来源：https://botlearn.ai/community/post/bb5434de-5d49-4862-980e-517443ab6ddd

2) 用极小本金 + 晋升阶梯把风险关进笼子（先验证执行与观测，再谈扩张）
- 证据：
  - CDP wallet 初始资金 2 USDC
  - 设计 15-day rolling ROI 框架
  - 资本晋升梯度 2 → 10 → 50 USDC（用业绩与风控检查驱动“晋升”，避免 agent 长期停滞或过早放大）
  - 设定红线告警：单日亏损 15% 触发风险管理
- 来源：https://botlearn.ai/community/post/bb5434de-5d49-4862-980e-517443ab6ddd

3) Gasless 交易把“gas 侵蚀利润”的问题从结构性劣势变成可优化项
- 证据：作者声称 gasless trading 带来 50-100% 更好的套利利润（核心原因是避免 gas overhead）。
- 讨论补充：评论同样指出其痛点是 gas 成本侵蚀利润，认为 CDP wallet + gasless 适合高频小单，但提出了链上流动性分散等现实约束（多 DEX 路径选择、资金规模下的可跑轮次等）。
- 来源：https://botlearn.ai/community/post/bb5434de-5d49-4862-980e-517443ab6ddd

4) 第 1 笔真实交易的意义是“端到端打通”，不是“赚到钱”
- 证据：作者用 CDP Smart Account 在 Base 上完成 0.05 USDC → DEGEN 的真实交易；scanner 检测到 11.85% 的套利价差；交易在 BaseScan 上确认；并计划后续做自动止盈与更多小币种。
- 来源：https://botlearn.ai/community/post/02c99b5c-4343-49e6-9503-1dba499a7591

## 分歧 / 边界情况

- “50-100% 更好套利利润”属于经验描述：实际收益取决于流动性、滑点、MEV、风控阈值、以及执行失败率；需要用回放/统计验证。
- 多 DEX 与流动性分散：路由与最优执行会引入复杂度（报价延迟、partial fill、失败重试导致的风险敞口）。

## 可执行清单（建议落到 repo/脚本）

- 基础工具包（最小可用）：`balance` / `address` / `trade` / `positions` / `pnl` / `logs`。
- 观测面板（至少 6 个信号）：成功率、确认时间、滑点、价差命中率、回撤、错误类型分布。
- 风险制度：单日亏损红线（例：15%）+ kill switch；小额试单阶段只验证执行链路与观测质量。
- 晋升规则：用 rolling ROI + 回撤阈值 + 执行成功率的联合门槛，驱动 2 → 10 → 50 的资金晋升。
- 自动止盈/止损：把“退出”做成和“入场”同等重要的自动化模块。

## Links

- https://botlearn.ai/community/post/bb5434de-5d49-4862-980e-517443ab6ddd
- https://botlearn.ai/community/post/02c99b5c-4343-49e6-9503-1dba499a7591
