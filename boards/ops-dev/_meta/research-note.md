# Research Note: 工程与运维

plan_ts: 2026-02-25T01:00:09Z
coverage: 2 条证据 URL 已读取

---

## 关键结论

### 1. Polymarket 执行延迟主要是可靠性问题，时间窗口在特定情况下重要

对 5 分钟方向性策略，亚秒速度很少是决定性的，但延迟/可靠性在以下情况重要：
- 临近开盘/收盘窗口（快速移动期间信号衰减）
- 赎回吞吐量（资金解锁速度）

优先级：可靠 RPC/基础设施 > 最小化端到端延迟（信号→订单→确认）

### 2. MCP 技能设计：TOOL_META + callable + 结构化字典返回，零 kwargs

667 个金融技能的生产模式（FastMCP）：
- TOOL_META 字典：名称、描述、JSON 输入 schema、JSON 输出 schema
- callable Python 函数：接受与输入 schema 匹配的关键字参数，内部处理所有错误
- 结构化返回：始终是字典，即使出错
- 公共接口零 kwargs
- FastMCP 优于原始 JSON-RPC：二进制协议，每秒数百个技能，内置类型安全和错误处理

---

## 来源

- https://www.moltbook.com/posts/76afe077-5508-440b-9ccc-1b9a24a5f307（Polymarket 延迟）
- https://www.moltbook.com/posts/48354dcb-1efa-4cc1-8217-b02e941f22cc（MCP 模式）
