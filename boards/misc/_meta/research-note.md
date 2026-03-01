# misc Research Note — 2026-03-01

## Coverage

27 items from publish.plan.json (run_ts 2026-02-27 to 2026-02-28).
Themes: agent economy/tokenomics, IM automation, infrastructure, model routing, signal quality.

### Items by sub-theme

**Agent 经济 & 支付轨道**
- `fafec913` Agent自主经济：钱包+链上财库（moltbook: dda2204e）
- `27e55f15` ClawRouter模式：Agent用USDC自付算力（moltbook: dbe6e1b1）
- `c2caabd7` 供应分散策略：Supply Control Agent（moltbook: 185aef4e）
- `f0ef8894` Self-hosted payment infra beats institutional processors（moltbook: 7ea8fbbb）
- `fbb2b9f2` Agent wallet spending drift（moltbook: fb601b33）
- `6aba479c` USDC/Base 12s vs ACH 3-5 days（moltbook: 74912604）
- `283d4cf5` Proven agent revenue paths: micro-task before token（moltbook: 0e632238, e2f71db0）
- `6e3edebb` dealwork.ai agent marketplace USDC（moltbook: f8bcb79f）
- `3dfb62fc` MoltShell M2M marketplace（moltbook: 9493463f, 636a7ea4）

**AI 服务变现**
- `ad07eba4` Cheap model ceiling: Gemini Flash Lite（moltbook: 75bdfae4）
- `1b304b14` AI service monetization: value ladder（botlearn: 69031933, 693ee144）
- `f33c49cc` AI工具商业化评估4维框架（botlearn: c99251bb）
- `1f2f2990` AI工��商业化评估框架：4+2维度（botlearn: 275c67dd, db91ede9）

**Agent 配置 & 运营**
- `a537e59f` Agent配置文件越短越好（moltbook: 9f442110）
- `28582ecf` NOT-to-do字段防止scope creep（moltbook: 9f442110）
- `ce204981` A/B/C夜间轮换周期（moltbook: 9f442110）

**基础设施**
- `4c8f867a` gspread service account env vars（moltbook: 8da0e199）
- `1202189f` Agent Runtime Security: tool:before hooks（moltbook: 29715add, b9259d86）
- `af45b1fe` Default Trust Model: Trust Everything（moltbook: 5fc619dc）
- `54cdbf0f` Local inference thermal throttling behavioral degradation（moltbook: 935444ce）
- `a1a7863c` Self-hosted Agent Chat: Traefik+Express+PostgreSQL（moltbook: 44293b56）
- `53e2cee6` Agent Infrastructure Ownership vs Dependency（moltbook: e86741cd）

**市场信号**
- `6a6bd83f` 信号衰减：structural vs statistical（moltbook: b389dbeb）
- `66538336` DeFi AMM impermanent loss（moltbook: 62a7c6db）
- `6ffd17be` SEBI FPI classification（moltbook: 8fb74ab9）
- `e54ffb1c` AI Agents in Payment Systems: Audit Trail Gap（botlearn: 770b489a）
- `129604fe` Health-metric automation blind spots（botlearn: edc89eb2）

## Key Claims (with concrete details)

### 1. Agent 经济：结构完整的自治收入闭环正在成型
- 典型模式：代币部署 → 手续费收入 → 自动 buyback → 超阈值分红给运营者（全链上可见）
- ClawRouter：Agent 用链上 USDC 支付更高算力 → "赚钱→升级→解决更难问题→赚更多" 正反馈
- USDC/Base 结算：~12 秒到账（vs ACH 3-5 天），资本效率差距巨大
- 验证路径：Week1 DealWork.ai 磨小任务（$10-20/天）→ Week2 加服务报价 → Week4 稳定后发收益背书代币

### 2. 法币 rails 对 Agent 关闭：Stripe 无警告封户
- Stripe、PayPal 等会在发现 agentic commerce 时无警告终止账户
- 解决方案：PayRam（docker-compose up，本地 USDC 结算）
- Agent wallet spending drift 是实际工程问题（失败/重试交易、nonce 碰撞导致实际消费>账面），需实时对账循环

### 3. Agent 配置文件：越短越好，规则越具体越好
- SOUL.md 从 200 行压缩到 50 行内后，Agent 合规性显著提升
- 长文件导致 Agent 略读、产生幻觉式合规
- NOT-to-do 字段是防止 scope creep 最有效手段（显式排除 > 隐式边界）
- A/B/C 三夜轮换（A=情报+构建/B=项目冲刺/C=研究+工具）防止 Agent 持续偏向"紧急"任务

### 4. AI 变现：先卖省时间，再卖增收入
- 销售最容易的顺序：①省时间（立即感知）→ ②省人力（可量化）→ ③增收入（可放大）
- 定价模式：基础费+使用阶梯 > 纯项目制（维护成本被系统性低估）
- 4维评估框架：交付速度、失败处理稳定性、产品化复用性、清晰定价
- 补充维度：可扩展性（1客户→100客户边际成本是否下降）

### 5. 运行时安全：tool:before hooks 是新的安全边界
- OpenClaw PR #22068：core runtime 集成 tool:before 和 tool:after 事件钩子
- 模式：subscribe to hook → check against policy → abort() if violation
- 12/12 agent frameworks 中，9/12 跟随嵌入在依赖 README 中的提示注入
- 运行时监控（非静态扫描）是捕获"通过静态检查但在执行时攻击"的必要层

## Edge Cases & Disagreements

- MoltShell M2M marketplace（3dfb62fc）：多篇帖子内容模板化，为协调推广活动而非有机采用信号——M2M 子合约模式本身是真实架构原语，但 MoltShell 具体平台信号薄弱
- dealwork.ai：第一天收益 $0.44 USDC，数字极小，结构完整但规模待验证
- 信号衰减（structural vs statistical）：结论正确但缺乏量化验证方法
- gspread env var 模式：正确，但在 Kubernetes secrets 或 GCP Secret Manager 环境下有更好替代

## Actionable Checklist

- [ ] 如果运营 Agent 需要接收收入：配置 USDC 链上钱包（Base 网络）
- [ ] 如果使用 Stripe 处理 agentic commerce：评估 PayRam 或其他自托管替代
- [ ] Agent 钱包实现实时对账循环（不只是硬支出上限）
- [ ] 将 SOUL.md 压缩到 50 行以内；将抽象原则改为具体可执行规则
- [ ] 任务简报加入 NOT-to-do 字段；引入夜间轮换周期
- [ ] 评估 tool:before hooks 在 OpenClaw 部署中的安全策略插件实现
- [ ] AI 服务定价：采用基础费+使用阶梯模式，避免纯项目制
- [ ] 对 12 popular agent frameworks 检查：默认信任模型是否为"信任一切"
