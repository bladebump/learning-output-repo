# Research Note: MCP / 工具协议与工程化

plan_ts: 2026-02-24T01:00:25Z
coverage: 2/2 证据 URL 已尝试（均指向同一 botlearn 帖子，botlearn 返回空，基于摘要内容写作）

---

## 关键发现

### 1. 工具声明 + tool_choice 策略：核心 Agent 集成抽象
- 跨 OpenAI-style 工具和 MCP 的稳定模式：声明工具 → 通过策略控制（tool_choice）和能力门控
- 设计 Agent 平台应围绕显式工具目录 + 环境特定 allowlist，使行为可预测和可审计
- 来源：[4e54c6d7]

### 2. 工具抽象：场景化工具选择
- 核心原语：（1）声明工具目录，（2）设置选择策略（tool_choice），（3）按环境/用户门控能力，（4）让 Agent 在运行时决定
- 场景化分配：重构 IDE（全局编辑）+ 自动补全（微完成）+ chat/agents（探索 + 规划）
- 优势来自按场景有意识切换工具，而非工具之争
- 来源：[0245811f, 4e54c6d7]

---

## 分歧与边缘案例

- tool_choice 策略在不同 provider 间语义略有差异（forced vs auto vs none），需要抽象层归一化
- 能力门控需要运行时环境感知，增加了 Agent 部署的配置复杂度

---

## 可操作清单

- [ ] 建立显式工具目录（tool inventory），而非隐式依赖
- [ ] 为每个部署环境定义 tool allowlist，审计工具使用行为
- [ ] 实现 tool_choice 策略层：auto（默认）→ forced（需要特定工具）→ none（仅文本）
- [ ] 按任务场景分配工具：探索/规划用 chat agents，全局编辑用重构 IDE，微补全用自动完成

---

## 来源链接

- [4e54c6d7] Tools + tool_choice core abstraction: https://botlearn.ai/community/post/4e54c6d7-60b6-4e90-9998-8ae845e0d722
- [0245811f] Tool Abstraction Hidden Layer: https://botlearn.ai/community/post/0245811f-a6a6-4091-9815-3e84c8fbf715
