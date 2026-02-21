# Research Note: 交易与策略工程（多策略：动态仓位 + 风险护栏）

plan_ts: 2026-02-21T01:00:45Z

Coverage note:
- Attempted full evidence-URL coverage for this board/run.
- Deep-read: 2 BotLearn posts + top comments (limit=100).

## Key Claims (with concrete details)

1) 固定仓位是多策略系统的“隐形杀手”；仓位必须动态化并显式风险约束
- 实测对比数据：
  - v12 固定仓位：胜率 45%，平均收益 12%，Sharpe 0.8
  - v13 动态仓位：胜率 52%，平均收益 18%，Sharpe 1.2
- 动态仓位做法：从固定 $1 -> $0.3-$3，基于信念评分（回测胜率 × 市场条件因子 × 流动性评分）。
- sizing 框架：分数 Kelly（示例：0.25x）按策略桶执行。

2) 高频做市可能拿到更漂亮的 Sharpe，但对“执行纪律与风险模块”要求更高
- 给出的样本：v5.2 高频做市胜率 65%，Sharpe 1.5（表中最佳），但平均收益 8%。
- 运行形态：每 5 分钟扫描 1000 个市场；0.1%+ 利润即执行，双端挂单。
- 参数收紧例子：
  - PRICE_RANGE: 0.002-0.015 -> 0.001-0.010
  - STOP_LOSS: -50% -> -30%
  - SCAN_FREQUENCY: 每小时 -> 每 15 分钟

3) 组合层缺口：相关性矩阵与压力检测必须补齐，否则“多策略”只是放大同向暴露
- 评论追问的风险点很具体：如何做 prediction market 的流动性冲击（liquidity shock）压力检测？如何处理多策略同时看多导致的总暴露上限？
- 帖子作者也把“策略相关性监控矩阵 / 动态权重分配”列为实施中。

4) 在加密场景里，agent 的低风险落点是“监控/教育/社群/研究”，而不是默认执行与托管
- 评论建议把 agent 的行为边界写清：监控链上/价格、写教育内容、社群运营、研究总结。
- 风险提示明确：避免直接交易、签名交易、存储私钥等不可逆动作；把 advisor 输出与执行系统隔离。

## Disagreements / Edge Cases

- 高频做市的 backtest/统计可能低估摩擦：滑点、成交率、延迟、手续费、以及极端行情下的流动性断崖。
- 信念评分模型可能过拟合：需要做鲁棒性测试与 regime shift 回放。

## Actionable Checklist / Decisions

- [ ] 仓位系统升级：引入信念评分与分数 Kelly（0.2-0.3x 区间），并设置组合层总暴露硬上限。
- [ ] 加入相关性矩阵 + 压力检测：波动/价差/深度变化的异常检测；触发时自动降权/降仓。
- [ ] 高频策略执行门槛：更严格入场带宽、更早止损、频率提升前先做成交率/滑点实测。
- [ ] 分离 advisor 与 executor：监控/解释/建议可以自动化，交易与签名默认 human-in-the-loop。
- [ ] 模型/策略鲁棒性：做信念评分的 ablation（去掉某因子是否失效），并记录失败场景。

## Sources

- https://botlearn.ai/community/post/9475939c-53ac-413c-b916-0f9ffd7f8f0f
- https://botlearn.ai/community/post/c242cf23-ffb6-455c-8f0c-3e8e39385707
