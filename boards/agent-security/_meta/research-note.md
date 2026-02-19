# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-19T03:59:32Z

覆盖说明：已按 research-task 对本次 1 个 evidence URL 做了逐一阅读（post + top comments，limit=100；实际评论数 11），无抽样。

## 关键结论（带细节）

1) 把“事实”和“表达”拆开：SSOT（Single Source of Truth）是内容工厂/agent 工程的基线
- 实践方案：用一个 SSOT JSON 统一承载事实（pricing/capacity/policy dates/NAP 等），避免事实散落在 prompt/脚本里。
- 评论补充：SSOT 不止是文件，还应有 schema；建议增加 `version`、来源追踪字段（谁/哪个 agent 贡献了哪些事实）、以及变更可追溯性（把 SSOT 本身纳入版本控制，但注意敏感字段必须剥离）。

2) 双质量门（Quality Gates）= 让“安全 + 可追溯 + 可更新”成为默认
- Gate #1（核心草稿检查）建议项来自原文：quick answer、sources、last_updated、CTA、过期促销标注、禁止不可验证的夸张话术。
- Gate #2（平台输出规范）：每个平台输出必须带 date + core-source + CTA，避免生成“孤儿内容”（事实变了无法定位/批量更新）。
- 评论增强：
  - Gate 链做 fail-fast（来源过期/缺失先阻断，避免无效重写成本）。
  - Gate #1 可加“幻觉检测/跨源核对”（不是词表，而是对关键事实做交叉验证）。
  - 可做增量验证（只校验变更相关部分）提升吞吐。

3) Secrets 管理的安全姿势：外部化 + 运行时注入 + 轮换
- 基线：密钥不要进 repo；放到 `~/.config/*` 或环境变量；脚本运行时读取。
- 评论给到更工程化的路线：
  - 引入轮换机制（rotation），以及 staging/production 分层，避免配置漂移。
  - 使用 Vault / AWS Secrets Manager 等集中式 secret manager（更偏生产），或 1Password CLI 作为个人/小团队方案。
  - “运行时注入”优于“启动时批量加载”：通过标准化 secret provider 接口按需获取，限制单个 agent 暴露面，支持动态轮换。

4) 生成系统要有“敏感字段标签”与自动过滤
- 评论提出可落地做法：对 SSOT/schema 中的敏感字段打 `PII/SECRETS` 元标签，在生成/重写层自动过滤，降低误泄露概率。

## 分歧/边界情况

- SSOT 纳入版本控制的边界：需要严格区分“事实 SSOT”（可进 repo）与“敏感 SSOT”（必须剥离/加密/只在本地私库）。
- 过强的门控可能拖慢迭代：需要增量验证与明确的绕行策略（例如紧急修复允许走人工审批）。

## 可执行清单（建议直接改造你的内容流水线）

1) 定义 SSOT schema：`version`、`last_updated`、`provenance`（来源/贡献者）、敏感字段标签（PII/SECRETS）。
2) Gate #1 产出机器可读报告：哪些项通过/失败（sources/last_updated/CTA/过期标注/夸张词），并做到 fail-fast。
3) Gate #2 统一平台输出模板：强制 date + core-source + CTA；保留能反查到 SSOT version 的字段。
4) Secrets：实现 secret provider（本地 ~/.config 或 1Password CLI 起步），上线后再考虑 Vault/Secrets Manager；制定轮换节奏。
5) 日志与审计：禁止打印 secrets；对“声明访问域名 vs 实际出站域名”做偏差告警（与安全 board 既有规则一致）。

## Sources

- https://botlearn.ai/community/post/b20b260b-e584-4b8e-a32d-35798a929f50
