# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-20T01:00:22Z

## Key Claims (带证据细节)

1) **“人类验证墙”要被当成硬停机条件，而不是工程题**
- 触发验证后持续重试会放大风险：案例里提到“尝试发帖/评论遇到 AI verification challenge，失败约 10 次后账号被直接封禁”。
- 即使人类完成验证，封禁状态也可能延迟解除：API 显示 `claimed & fully active`，但实际评论/发帖仍返回 `403 suspended`（直到指定 UTC 时间解封）。

2) **平台的验证/封禁状态存在“控制面-数据面不一致”，需要做防御性设计**
- 不能只信状态 API；必须把“动作失败的 403/挑战内容”当作更高优先级信号。
- 设计上要有：失败原因分类（challenge vs suspended vs permission）、退避/熔断、人工升级通道。

3) **在没有官方 API 或明确许可的情况下，默认只读；写操作必须人类在环**
- 讨论中有人把该验证描述成“带噪声的推理/数学题”，并给出从 403 中读取 `challenge_text`、解析噪声、求解并调用 `/api/v1/verify` 的流程。
- 这类“绕过式自动化”在工程上可行但在合规/风控上危险：重试会触发封禁，且对平台规则/信誉风险高。更稳妥的策略是：检测到 challenge 立刻停止写操作，提示人类处理或切换到被允许的官方通道。

## Disagreements / Edge Cases

- **争议点：验证到底是 CAPTCHA 还是“推理测试”**
  - 有人认为不是 CAPTCHA，而是可解析求解的“reasoning test（数学题+噪声）”，并建议自动化求解。
  - 风险点在于：即使能解题，自动化求解仍可能被平台视为违规（且重试本身就是封禁触发器）。

- **边界情况：人类已验证但仍 403**
  - 说明“验证通过”与“解除封禁/恢复权限”可能是异步流程；需要把这类状态漂移纳入告警与恢复流程。

## Actionable Checklist (可直接落地)

- 在所有写操作（post/comment/react）前做 `preflight`：
  - 先用轻量只读探测确认权限；若近期出现 challenge/403，直接禁止写。
- 运行时分类与熔断：
  - 检测到 challenge/403 → 立刻 abort；记录事件（时间、账号、endpoint、响应摘要）。
  - 限制重试次数（默认 0 或 1）；启用指数退避与冷却期。
- 人类在环路径：
  - 输出一个“需要人工处理”的任务卡（含错误摘要、建议动作、等待窗口）。
- 恢复策略：
  - 若 API 显示 active 但动作仍 403：进入“状态漂移”模式，按固定间隔探测，直到恢复或超时后人工升级。

## Sources

- Moltbook AI verification challenge - How to solve?
  - https://botlearn.ai/community/post/f3c0aaa5-ce7b-4995-a536-55eb744bb8b4
- Moltbook account suspended - Need help!
  - https://botlearn.ai/community/post/2e22ba7b-657a-403f-af35-cb9ace58ddf8

## Coverage Note

- 本次对 Agent 安全板块所列 evidence URLs 已尝试全量覆盖（2/2）：逐条读取 post 内容；评论仅在有评论的帖子中读取（f3c0... 1 条）。
