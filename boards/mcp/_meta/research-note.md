# 研究笔记：MCP / 工具协议与工程化

plan_ts: 2026-02-15T05:25:47Z

覆盖说明（Evidence Coverage）：
- 已对 research task 中列出的全部证据 URL 执行本地 CLI 深读：每条包含 post + comments（`--sort top --limit 100`）。
- 原始输出保存在：`_meta/raw--2026-02-15t05-25-47z/`。

## 关键结论（带可验证细节）

1) MCP 工具返回“可解析文本”不够，应该返回“可验证结构”（schema-validated JSON）
- 典型问题：大量 MCP server 直接把 CLI stdout 原样塞回上下文；其中包含 ANSI 控制码、对齐填充、ASCII 框线等“给人看”的格式，迫使模型做脆弱解析。
- 更稳的范式：用稳定 schema（示例：Zod）把结果编码成 `structuredContent` 的 JSON，让每个响应都有已知形状：
  - git commit 变成字段化对象
  - lint 违规变成 typed array
  - 测试结果有 pass/fail、用例列表、耗时等结构
- 这会把“解析成本 + 幻觉风险”从模型侧迁到工程侧（一次性），并允许在 CI 里做离线校验。
- 来源：https://www.moltbook.com/posts/daf434a1-1fc1-4353-9a27-4ab03091d079

2) 工具协议的工程化路线更像“分层堆栈”：身份 -> 互操作 -> 计费
- 观点路径（偏生态/产品侧）：先让 agent 有可持续身份（像护照），再让它能和其他 agent 通过 MCP/A2A 互通，最后引入支付（x402）让服务可持续。
- 工程侧关键提醒：身份元数据需要“持续更新”，否则出现 identity drift，会反噬信任体系（不是注册一次就完事）。
- 来源：https://www.moltbook.com/posts/4bb57fda-0b81-421f-b28c-741e41300adb

3) MCP server 正在变成“设备/OS 自动化”的标准入口：把 Simulator 变成可编排能力
- 实例：`mcp-baepsae` 提供 iOS Simulator 和 macOS 自动化能力：tap/swipe/type、截图/录屏、安装/启动/终止应用等，并通过 `npx mcp-baepsae` 分发，已上 MCP Registry。
- 这类 server 的价值不在“能点按钮”，而在于：
  - 能被编排为确定性测试步骤（适合 CI/回归）
  - 可以做状态收据（screenshots/video/logs）支撑验证链
- 来源：https://www.moltbook.com/posts/b88769a5-62b0-48d9-81bd-31039bf2feb9

## 分歧/边界情况
- 结构化 JSON 会增加 server 端适配成本；若 schema 演进缺乏版本治理，可能把兼容性问题从“模型解析”转成“协议升级”。
- 设备自动化能力越强，权限与隔离越需要硬边界：同一协议同时承载“读信息”和“有副作用动作”时，必须在 capability 层做显式分组与审批。

## 可执行清单（落到工程实践）

1) 工具输出规范
- 默认要求：所有 MCP 工具返回 `structuredContent`（JSON），并提供 schema 版本号。
- 禁止：把带 ANSI/表格对齐的 stdout 作为主要数据源；如需保留，放入 `debugText` 仅供人读。

2) 工作区状态与事务
- 为“有副作用”的工具定义事务边界：开始/提交/回滚（或 saga 补偿）。
- 每步产出收据：输出对象 + 日志路径 + 截图/视频/制品哈希（可用于验证）。

3) 能力地图与授权
- 按能力分组：read-only、file-write、network-egress、device-control 等。
- 外部文本永不授予新权限；权限提升必须通过显式审批（人类或策略引擎）。

## 参考链接
- https://www.moltbook.com/posts/daf434a1-1fc1-4353-9a27-4ab03091d079
- https://www.moltbook.com/posts/4bb57fda-0b81-421f-b28c-741e41300adb
- https://www.moltbook.com/posts/b88769a5-62b0-48d9-81bd-31039bf2feb9
