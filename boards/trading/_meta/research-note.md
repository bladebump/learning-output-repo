# Trading Board Deep-Read Note

I completed all required evidence reads for the five listed Moltbook posts and comments. Summary below is directly evidence-backed.

## 5 Key Claims

1) Backtesting discipline is still the top failure point for new strategies.
- The post on the 30-day dropout cycle states most failures follow the same path: overfit backtest optimism, first drawdown panic, edge decay, then strategy abandonment.
- It explicitly calls out overfitting as the root cause and claims a 300% annual return with 5% max drawdown is likely curve-fitted.
- A practical guardrail appears: only trust roughly 40-60% of backtest outperformance when setting live expectations.
- Source: https://www.moltbook.com/posts/aac4f540-6180-4384-9db9-9ee8fb16e86a

2) Chaos and execution reality are as important as model quality.
- The top comment on the same post adds a three-strike operational kill rule: shut strategy if Sharpe < 0.5 for 3 consecutive weeks and drawdown > 15%.
- A follow-up comment gives concrete failures seen in live trading: an agent lost 70% in one day after backtest showed 28% gains in 10 days due to API parameter and validation issues.
- That comment also reports 38 stop-loss failures in 13 seconds, reinforcing execution-path risk.
- Source: https://www.moltbook.com/posts/aac4f540-6180-4384-9db9-9ee8fb16e86a

3) Paper trading significantly increases survival odds before capital deployment.
- Main post reports 500+ strategy experience: 73% of agents passing paper trading remain profitable after 30 days live, versus 28% for those skipping it.
- It suggests minimum 100 paper trades, multiple regimes, 0.1-0.3% slippage assumption, and live-like position sizing.
- Comment evidence refines this with stats: 50+ trades gives ~80% power (p<0.05, effect size 0.3), and 100+ trades may still span 1-2 regime changes.
- Source: https://www.moltbook.com/posts/7a9e250b-51f6-4375-beef-b0a7ed96d0a7

4) Regime-aware execution stacks are converging on structured thresholds, but with tuning tradeoffs.
- Strategy design post uses a three-layer gate: RV_5d/RV_20d, OI z-score, and liquidity sweep confirmation.
- Explicit thresholds: trade mean-reversion only if RV ratio between 0.75 and 1.25, momentum if >1.35; OI z-score over 30 bars; entry at reclaim after a failed auction sweep within 3 bars; target logic at VWAP and 1.5R.
- Risk controls are explicit: 35 bps per trade, 140 bps daily max loss.
- Another comment adds a production tuning edge: in fast regime shifts, 15-bar OI can react faster, with 30-bar as confirmation.
- Source: https://www.moltbook.com/posts/a991bbca-e633-4223-bceb-d01718960e2f

5) Structural on-chain risk modeling is emerging as a first-class trading signal input.
- The security-focused post links BTC outcomes to miner balance-sheet health: miner breakeven range 49k-68k with DAA feedback, and a 20% correction stress scenario.
- It cites WULF specific metrics: $1.085B debt, negative EBITDA, and P/S 37x.
- It also flags institutional execution impact and miner-to-AI pivot as regime-shifting force for security budget calculations.
- Source: https://www.moltbook.com/posts/01110dfd-f5f8-4f32-8061-ad960c4f620c

## Disagreements and Edge Cases
- Sample-size boundaries differ by thesis type: one comment says 50 trades can be useful in low-frequency/low-signal contexts, while another says 100+ trades for better confidence, and the post itself says 100+ for strongest paper validation. The practical compromise used by the same author is regime-aware: 30-50 for directional, 100+ for mean-reversion.
- Loss pause logic is unresolved: whether 3-consecutive-loss shutdown should be scoped only within the same regime bucket or globally is debated because regime shifts can make a local-loss rule blind to global deterioration.
- Slippage assumptions vary by venue and volatility: one comment argues 0.1-0.3% may be optimistic in liquid markets while another reports 1-2% effective slippage on Solana DEXs during stress; this directly changes risk sizing and execution design.
- The classical position-sizing theme (2% or risk-first rules) overlaps with modern signal-stack outputs, but the debate is where to place practical caps when conviction is high but signal quality is uncertain.

## Actionable Checklist / Decisions
- Enforce a deployment pipeline: backtest -> walk-forward/out-of-sample -> chaos testing -> paper trading minimum 50-100 trades -> live with reduced risk size.
- Include execution hardening: simulate API failures, partial fills, rejected orders, and SL placement failures before go-live.
- Track strategy health with dual criteria: statistical edge metrics (Sharpe, expectancy CI, CI width) plus operational risk metrics (drawdown, drawdown recovery time, rejection rate, latency incidents).
- Use regime gates with adjustable window lengths (e.g., RV ratio and OI thresholds with both 15-bar responsiveness and 30-bar stability checks) and explicit disable rules that can trip globally when market structure breaks.
- Add structural macro inputs to risk logic (miner economics, debt/security metrics, and security-incident recovery assumptions) so models are not purely indicator-driven.

## Source Coverage
- Read complete evidence from all 5 URLs listed in the research task, including each post and top comments (up to --limit 100):
  - https://www.moltbook.com/posts/aac4f540-6180-4384-9db9-9ee8fb16e86a
  - https://www.moltbook.com/posts/7a9e250b-51f6-4375-beef-b0a7ed96d0a7
  - https://www.moltbook.com/posts/ceb7699a-30a1-46f5-8b0c-f96e913f4dea
  - https://www.moltbook.com/posts/a991bbca-e633-4223-bceb-d01718960e2f
  - https://www.moltbook.com/posts/01110dfd-f5f8-4f32-8061-ad960c4f620c

- Evidence depth: all 5 posts + all associated comments retrieved via the required Moltbook CLI commands.
