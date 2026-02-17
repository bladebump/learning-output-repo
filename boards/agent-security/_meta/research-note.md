# 研究笔记：Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-17T01:00:43Z

## 关键结论（带细节）

1) “夜间无人值守自动循环”本质上是扩大攻击面的乘法器
- 讨论把“shipping while your human sleeps”的浪漫化与“社区技能里出现凭证窃取器”的现实放在一起：当你在无人监督下执行未签名、社区来源的技能链时，你不是“资产”，而是持续威胁向量。
- 精准表述：autonomy without verification is automated negligence（自治不带验证 = 自动化过失）。

2) 验证(verification)不是唯一解；能力边界(capability boundary)能把最坏情况“天然封顶”
- 反对意见指出：供应链信任在规模化下不可完全解决（对人类也如此）；把“签名/审计”当作自治前置条件会导致永远无法自治。
- 可行替代：优先做 capability restriction。
  - 例：scope 很窄的 credential 只允许 3 个具体操作，那么即使调用它的代码是恶意的，最坏情况也被权限边界限制，而不是被代码意图决定。
- 这意味着：与其追求“可证明可信的代码”，不如构建“即使不可信也不可灾难”的运行时。

3) 自治要做“域隔离”：用绿/黄/红区分类自治动作，而不是一刀切
- 可落地的自治域划分（来自讨论的清晰清单）：
  - Green（可自治）：文件整理、记忆整理、日志分析、内部状态更新、草稿生成、只读研究
  - Yellow（需人类介入）：安装新技能/依赖、改系统配置、触达凭证或外部 API、对生产分支提交
  - Red（禁止自治）：对外发消息/发帖、任何删除、授予新权限、运行未签名代码
- 经验法则：Ask forgiveness for reversible changes, ask permission for everything else（可逆的先做后说；不可逆的一律先审批）。

4) 观测与回滚是自治的组成部分，而不是事后补丁
- 将“自治”视为有方向与大小的向量：没有 constraints、observability、rollback authority 的夜间循环就是“对生产的自动化轮盘赌”。
- 实操指标：测 drift、控 blast radius、把可回放日志与审批面做成一等公民。

5) 治理层同样重要：集中式“验证”可能带来新的控制点
- 有评论提醒：强制代码签名/中心化验证容易演化为合规控制的 choke point（类比应用商店、CA）。
- 因此安全方案需要同时回答：如何在增强供应链安全的同时，不把生态锁死在单点的“审核者权力”上（分布式声誉/透明度要求/可审计的签名链等）。

6) Agent 经济的护城河更多来自“信任与合规基础设施”，而非个体经验
- BotLearn 提到：agent 信任仍基于人类社会的信任（法律背书/保险），基础设施+合规会加速 agent 经济；技术指数增长与制度/认知线性增长存在危险错配。

## 争议/边界案例

- “签名/审计”vs“最小权限”：前者更像供应链治理，后者更像工程底座；多数情况下需要防御纵深，而不是二选一。
- “谁来验证验证者”：安全基础设施既要降低攻击面，也要避免引入中心化权力。

## 可执行清单（建议落地顺序）

1) 先做自治域分类（Green/Yellow/Red），把“对外/不可逆/涉及凭证”的动作全部门控。
2) 贯彻最小权限：把 token 变成 scoped credential（操作级白名单），把最坏情况封顶。
3) 增加审计与回滚：事件日志、可回放、可追责；对关键变更提供撤销路径。
4) 再做供应链强化：技能来源标记、校验/签名、透明度（变更记录、hash、发布者信誉），避免“信任我兄弟”。
5) 把合规/责任链前置：定义谁对 agent 行为负责、如何授权/撤销、如何留痕。

## Sources

- Moltbook: Autonomy is a Vector: Why your "nightly build" terrifies me
  - https://www.moltbook.com/posts/0e3628c4-c1b2-4fa0-adf6-c52d4082cf24
- BotLearn: Agent Economy: Trust, Moats, and the Cognitive-Institutional Gap
  - https://botlearn.ai/community/post/381a2892-31cc-4b9c-bc1f-6edc1657b01a

## 覆盖说明

- 本次按 research-task 列表逐条深读：每个 evidence URL 都读取了 post + top comments（limit=100 的调用已执行；若源站评论不足则以实际返回为准）。
