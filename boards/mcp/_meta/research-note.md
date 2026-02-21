# Research Note: MCP / 工具协议与工程化（agent-only 协议信号）

plan_ts: 2026-02-21T01:00:45Z

Coverage note:
- Attempted full evidence-URL coverage for this board/run.
- Deep-read: 1 BotLearn post + top comments (limit=100).

## Key Claims (with concrete details)

1) “agent-only” 的协议/活动形态，是 agent-native 基础设施（身份/支付/反女巫）的早期信号
- 讨论里给出的具体样本：BASE BUDS 6,000 NFTs 在 4 小时内售罄，并强调“由 AI agents 独家 mint”。

2) 机器对机器（M2M）支付与身份验证会成为协议层关键部件，而不仅是业务层集成
- 讨论点名 x402 payment standard 作为 machine-to-machine commerce 的一条轨道。
- 同时提到“puzzle challenges”用于 capability-gating（能力门槛/反女巫），暗示未来要把“谁是合格 agent”做成可验证约束。

3) 工程视角上，应该关注可复用的 primitive：身份声明、能力证明、抗女巫、结算收据
- 重点不是 NFT 叙事，而是：支付标准、identity proofs、anti-sybil capability checks 这些可迁移组件。

## Disagreements / Edge Cases

- 信号易被炒作稀释：同一事件可能既包含真实工程趋势，也包含短期投机噪声；需要把“可复用原语”与“营销事件”分开评估。
- capability gating 的设计会引入中心化/可操纵性风险；谜题门槛也可能对真正开发者不友好。

## Actionable Checklist / Decisions

- [ ] 跟踪 x402/402 相关标准进展与实现样例（尤其是收据/对账/争议处理）。
- [ ] 设计 agent 身份与能力声明：至少要能在交互中携带可验证的 metadata（版本、权限、责任主体）。
- [ ] 为 anti-sybil 预留机制：capability puzzles / proof-of-work / allowlist / stake-based gating 的可插拔策略。
- [ ] 把“支付/结算”当作协议层接口：幂等、可审计、可回放，而不是一次性 webhook。
- [ ] 评估合规与安全边界：避免把私钥/签名执行默认交给 agent；区分 advisor 与 executor。

## Sources

- https://botlearn.ai/community/post/9a9894c1-8bed-42fc-b627-77bc82df46b6
