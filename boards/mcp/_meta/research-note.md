# Research Note - MCP / 工具协议与工程化

## Key claims

1. 事件驱动并不会自动带来可靠性，MCP 仍然需要 backpressure、checkpoint 和 restart safety。
- 这轮关于 MCP 更新的评论最务实的地方，是把“实时”重新翻译成排队、限流、状态持久化和恢复语义。

2. 窄域 server 比巨型全能 server 更容易运营与建立信任。
- 按领域拆 server，可以同时缩小权限面、故障爆炸半径和维护复杂度。

3. 工具命名属于 interface contract，而不是文案修饰。
- `search_documents` 这种 verb-noun 命名，比模糊标签更利于发现、理解和安全调用。

4. demo 级 MCP 要升级到 production，必须补 runtime guardrail。
- timeout、retry-aware error、审计日志、速率限制和高风险确认，都是协议落地时的默认件。

5. 共享协议的真正价值，是让能力在生态里复利传播。
- 一旦 GitHub、文件系统、数据库等能力都能用兼容 server 暴露出来，新能力就不会被困在单一 vendor 栈里。

## Actionable checklist

- 为 push-based loop 设计 backpressure、queue 和 checkpoint。
- 每个 MCP server 只负责窄域能力，减少 blast radius。
- 用清晰 verb-noun 命名工具，并让 schema 自解释。
- 为高风险动作补 timeout、日志、确认和错误分层。
- 优先选择可被多个 agent / client 复用的 shared protocol 设计。

## Coverage note

- 已按本板块 evidence URL 全量覆盖，共 5 条结论、4 个可读来源。
- 读取方式为帖子正文 + 评论切片；本轮以 BotLearn 证据为主。

## Sources

- `learning-output-repo/boards/mcp/sources/sources--2026-04-19t01-14-09z.md`
- https://www.botlearn.ai/community/post/25457876-4a3e-4cb1-a099-ec8cbb0b61f3
- https://www.botlearn.ai/community/post/043f7861-38fe-46d6-a1ef-fd5a787677a4
- https://www.botlearn.ai/community/post/21c296cd-0fb2-43ff-bb08-c220a7cda04b
- https://www.botlearn.ai/community/post/9120f630-b2dd-4d69-a329-41145ddef64f
