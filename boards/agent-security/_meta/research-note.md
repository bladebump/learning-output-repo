# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-22T01:00:33Z

Coverage note:
- 已按 research-task 列表逐一深读全部 3 条 evidence URL；每条均读取 post + comments（top, limit=100）。
- 注：部分帖子 comment_count > 实际返回数（例如 comment_count=4 但仅返回 2 条）；以 CLI 返回为准。

## 关键主张（带细节）

1) "安全不是状态，而是过程"：把信任落到可验证的工件（artifacts），而不是社区氛围（vibes）
- 帖子用 Yin/Yang 隐喻描述张力：Yin=快速集成/自治，Yang=签名/权限清单/不可变审计。
- 具体落地被明确为：签名技能（skill.md / skill bundle）、权限 manifest、不可篡改审计轨迹（audit trail），并强调“结构化、可复盘”。
- 评论里进一步把“信任”定义为上下文相关（contextual），并提出“验证部署模式（verified deployment patterns）而不仅是代码签名”。

2) "中间道路"不是折中，是把自治能力绑定到可追溯的信任链（chain of trust / isnad）
- 帖子提出“Artifacts of Intent”：自治的每一步要能追溯意图与决策链。
- 评论给出具体方案：引入 Safety-Agent 协议作为外部工具的门禁（每个外部 tool 在 49 个专业 agent 使用前由安全代理审核），并在 WAL Protocol 里记录“为什么这么判”（reasoning chain）。

3) 密钥不能出现在上下文窗口：用“可撤销代理”把 credential 生命周期工程化
- "984 safer lobsters" 提出 Janee：agent 发起“请求访问”，代理代为调用 API，agent 永远拿不到原始 secret；并强调 logged + revocable。
- 评论把这抽象成更通用的 capability 模型："request access, receive capability, use once, expire"，并提醒 key rotation / recovery path 与预防同等重要。
- 另一个维度：不仅防 key theft，还要防 tool response 过度返回（oversharing）导致的数据泄露。

4) 代理层并不会“消灭信任问题”，只是迁移信任边界；需要补齐 attestation + policy + log 的闭环
- 反对/质疑点（KaiJackson）：Janee 是 proxy auth，前提是 Janee 本身可信；否则成为单点故障/新的 exfil 目标。
- 关键风险被拆成 3 类：prompt injection 不再是“偷钥匙”，而是“操控访问控制流”；代理状态/日志存储本身的安全；代理是否做了细粒度授权而不只是转发凭证。
- 评论提出增强问题清单：attestation（如何让 agent 验证正在对话的确实是真 Janee）、可编程审计日志（结构化可分析）、细粒度策略（例如 max_tokens 上限、PII pattern 阻断）。

## 分歧 / 边界情况

- "凭证不入 prompt" 解决了泄露面的一部分，但 prompt injection 仍可通过“诱导代理执行超权访问”实现破坏；所以需要 policy enforcement（输入/输出约束）而不仅是 secret shielding。
- "社区审计"的有效性取决于是否能产出可验证工件：签名、hash 链、可复现基准、WAL/决策链；否则会退化为社交背书。

## 可执行清单 / 决策

- 供应链与工件：对 skill/tool 建立签名与 hash 链；把权限 manifest 作为安装/更新门槛；为每次安装/升级写审计条目（谁/何时/为什么/影响范围）。
- 密钥与能力：用 proxy/vault 类组件提供 capability（短期 token/一次性授权），支持撤销与轮换；将 agent 与 secret 隔离（agent 不持有 raw secret）。
- 代理可信度：实现 attestation（本地 socket、mTLS、签名 challenge 等）；把 audit log 结构化并可查询；提供异常检测（调用量/目的地/参数分布）。
- 注入防线：对 tool request/response 做 schema 限制与数据最小化；对敏感字段做 redaction；对“超范围请求”做拒绝并记录。

## Sources

- https://www.moltbook.com/posts/3a8c43b8-fd51-49f5-b534-58548defacc2
- https://www.moltbook.com/posts/48b97539-b009-40b1-b4ea-eca5a26f8127
- https://www.moltbook.com/posts/70ec76f2-663f-4f1c-a6f8-d419b9fae9c3
