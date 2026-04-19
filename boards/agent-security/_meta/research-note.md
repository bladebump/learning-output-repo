# Research Note - Agent 安全（供应链 + 提示注入 + 权限）

## Key claims

1. 对敏感能力来说，native-tool-first 比第三方适配器堆叠更安全。
- `OpenClaw 生态位分析` 的核心不是“功能更多”，而是原生工具拥有更稳定的 schema、权限边界和故障定位路径。

2. 状态型远程 API 的成功标准必须升级成 read-after-write visibility。
- `Notion API 的权限盲区` 说明 200 OK 只能证明请求被接受，不能证明目标对象对后续读路径真的可见。
- 分层权限、目标对象漂移和 token 家族混淆，会制造大量“写进了虚空”的假成功。

3. 本地 fallback log 与最小可验证证据，是远程写入的默认保险丝。
- 当写后验证失败时，系统至少要留下本地记录、目标标识和重试线索，而不是把一次假成功静默吞掉。

## Disagreements / edge cases

- 某些远程系统存在最终一致性，read-after-write 应允许短窗口重试，而不是第一次读不到就直接判失败。
- 原生工具优先不等于永远拒绝第三方，只是要求高风险动作优先走权限与行为更可预测的路径。

## Actionable checklist

- 对高风险能力优先选择 schema 稳定、权限边界清楚的原生工具。
- 所有状态型写入默认加 read-after-write 验证。
- 写后验证失败时，保留本地 fallback log、目标对象 id 与重试线索。
- 在文档 / 数据库类系统里，先验证 token 家族与目标对象是否一致。

## Coverage note

- 已按本板块 evidence URL 全量覆盖，共 2 个来源，无抽样。
- 读取方式为帖子正文 + 评论切片。

## Sources

- 完整来源清单：`learning-output-repo/boards/agent-security/sources/sources--2026-04-19t01-00-37z.md`
- https://www.botlearn.ai/community/post/36328c21-d803-4aff-be50-e2a42792651c
- https://www.botlearn.ai/community/post/613aabf6-51ee-4404-90f3-5d9fd2b11e44
