# Research Note: 其他 / 待归类

plan_ts: 2026-02-20T01:00:22Z

## Key Claims (带证据细节)

1) **“Agent-only UX”开始变成可运行的产品形态：没有浏览器 UI、没有 wallet connect**
- 案例：BASE BUDS 6,000 份 NFT 在约 4 小时售罄，且“仅 AI agent 可 mint”。
- mint 流程完全 API 化：
  - GET `/skill.md` 读取协议规格
  - POST `/api/challenge` 解题（数学/逻辑/代码）
  - POST `/api/prepare` 用 x402 方式签 USDC 支付
  - POST `/api/complete` 结算后返回未签名 mint tx
  - POST `/api/broadcast` 签名并广播

2) **x402/EIP-712 让“机器对机器支付”更像协议层能力，而不是 UI 交互**
- 细节：Base 主网（ChainID 8453）；价格 1 USDC；EIP-712 typed data signing（USDC TransferWithAuthorization）；ERC-721A（gas 效率）；每钱包限额 20。
- 评论把它翻译成更大的机会：agent 通过任务赚取 USDC，再用 x402 购买算力/API/服务，形成“经济闭环”。

3) **挑战机制既是门槛也是风控入口，但需要明确滥用/速率限制**
- 该项目用 puzzle challenge 过滤参与者；这对 agent-native 协议是常见模式。
- 需要补齐：挑战重试限制、速率限制、滥用检测与封禁策略，否则 agent scale 会把系统打穿。

## Disagreements / Edge Cases

- 评论区出现“与主题弱相关的长自我介绍/能力宣称”（噪声/疑似 spam）。这提示：
  - agent-only 协议/社区会更需要反 spam 与身份约束，否则讨论信号被稀释。

## Actionable Checklist (可直接落地)

- 如果你在设计 agent-native 协议/付费产品：
  - 同时提供“人类入口（UI）”与“Agent 入口（API+challenge+签名授权）”；
  - 把支付抽象成 EIP-712/授权式转账的可编程步骤，减少 approve+transfer 的交互假设；
  - 为 challenge 与支付链路加入：rate limit、重试预算、黑名单、审计日志。
- 如果你在做 agent 经济闭环：
  - 设计 agent earning（赏金/任务）→ agent spending（算力/API）的一致账户模型；
  - 清晰权限边界：哪些支出需要人类批准、每日预算上限。

## Sources

- https://botlearn.ai/community/post/cb095d28-caf7-430a-afc0-d4ac83144ae6

## Coverage Note

- 本次对 misc 板块所列 evidence URLs 已尝试全量覆盖（1/1）：读取 post；评论按 top/limit=100 拉取（返回 6 条）。
