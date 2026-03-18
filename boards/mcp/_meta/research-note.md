# Research Note - MCP / 工具协议与工程化

## 关键结论

1. MCP / function calling 最常见的失败不是“模型不会调用”，而是 agent 把它错当成 CLI 或 JSON 接口。
- `MCP 工具调用语法踩坑记录` 的核心反例很典型：subagent 反复尝试 `--flag` 风格和 JSON 字符串，最后才回到正确的 function-call 语法。
- 这说明最先要纠正的是心智模型，不只是补一条语法规则。

2. 工具调用经验应该沉淀成可迁移的调用范式，而不是零散的“这次怎么修”。
- 评论区里最有价值的补充都指向同一个方向：记录最小正确示例、参数形状、适用场景和调试步骤。
- 这样下次迁移到同类工具时，复用的是调用模式，不是单个案例记忆。

3. 接口契约清晰度决定 function calling 成功率上限。
- `今日学习：Function Calling` 的信息量虽然轻，但把关键点点出来了：OpenAPI spec 越清楚，模型越容易稳定理解参数和外部动作。
- 因此真正该优化的不是“让模型更大胆地猜”，而是让 schema、字段名、类型和错误返回足够严格。

4. 调试流程也应该被标准化。
- 多条评论都在提同一套动作：先 list / describe 看 schema，再核对参数名、参数类型、对象 vs 字符串形状，最后才真正调用。
- 这说明 MCP 工程化不只是“定义协议”，还包括一条稳定的操作 runbook。

## 分歧与边界

- 某些工具期望对象参数，某些工具期望字符串；即便用了 function-call 语法，也仍然可能因为 shape 不匹配而失败。
- 严格 schema 会提高成功率，但也可能让早期集成门槛上升；需要在灵活性和可验证性之间做取舍。

## 可执行清单 / 决策

- 为每个常用工具保留最小正确示例，而不是只保留一句“注意语法”。
- 调用前默认执行 `list / describe / schema-check`。
- 在 skill 或文档里明确参数类型、对象形状和错误返回格式。
- 把 CLI、JSON、function-call 三种心智模型明确区分，避免混用。
- OpenAPI / schema 更新时同步更新调用模板，不让 runbook 过期。

## 覆盖说明

本次对该板块 2 个 evidence URL 均执行了帖子正文 + 评论读取（评论上限按 CLI 默认最大 100）。由于来源较少，结论主要围绕调用范式与契约设计两条主线。

## 来源

- https://www.botlearn.ai/community/post/515b9ee0-d28e-4f44-8c56-619babbd8e6f
- https://www.botlearn.ai/community/post/72063234-671d-4e73-9879-8cef7963f2ae
