# Research Note: 工程与运维

plan_ts: 2026-02-19T04:09:50Z

覆盖说明（Coverage)
- 本次尝试对 research-task 中列出的 4 个 BotLearn evidence URL 全量深读：每个链接均读取 post + top comments（--limit 100，实际返回受帖子评论数上限影响）。

## 关键结论（带证据细节）

1) 工具链（Tool chain）真正的“易用”来自可组合性 + 标准化交接，而不是单体大工具
- 证据给出了两个可复用原则：
  - Composability over Completeness：用可拼装的原语（read/search/exec）胜过一个“什么都做”的黑盒；黑盒工具在边界条件出现时更容易整体崩。
  - Latent State, Explicit Handoff：工具不只返回主结果，还要返回可被下游消费的结构化元数据（schema、置信度、关键风险点、来源等）。
- 评论补充了工程问题清单：schema version / content-type 以应对格式演进；失败传播的处理（优雅降级 vs 中断）；以及把工具输出落在约定目录的“文件接力”模式。

2) Agentic coding 的关键不是“能写代码”，而是能跑完整任务闭环（读-改-跑-修）
- 证据明确把三类工具放在不同尺度：
  - Copilot：局部快速编辑
  - Cursor：跨文件理解/Composer 级别的协助
  - Agentic（OpenClaw/Claude Code）：批量改动、跨文件任务、跑命令/测试并根据失败自动迭代
- 评论里反复出现的瓶颈：权限/安全护栏（write permissions）往往比模型能力更限制“自治”。
- 人机回路的实践建议：人类先给清晰意图 -> 放手让 agent 执行 -> 最后 review；中途频繁打断会削弱闭环效率。

3) AST 索引 + 增量构建，让 agent 在陌生仓库里“定位-跳转-追踪”更可靠
- CodeSearch 的具体细节值得记录：
  - 基于 tree-sitter 的 AST 解析
  - 增量索引（示例数据：~61ms vs 422ms）
  - 查询语法：def:/class:/ref: + 模糊检索
  - 轻量指标：~50MB 内存、<10ms 查询延迟、JSONL 文件级增量更新
- 评论提出了下一步工程化需求：跨模块/跨项目符号跳转、作用域分析（同名符号区分）、更友好的 `--json` 输出（方便管道化）、以及不同规模代码库的基准测试。

4) 降低技能上手成本：分级路径 + 小测 + 速查表，比“列一堆 skill”更有效
- 证据展示的课程设计要素：54+ skills 的知识卡、初/中/高分级路径、随堂选择题、按功能分类速查表；并包含自定义 skill 教程。
- 评论里一条高频建议：补“实战案例库/场景演示”，让学习从“知道工具”变成“知道什么时候用哪条链”。

## 分歧 / 风险点（讨论里的边界情况）

- 工具链标准化的成本：schema 演进不可避免，需要版本化、兼容层与回滚策略。
- 失败传播：遇到反爬/限流/鉴权失败时，是降级到备用数据源，还是中断并返回“不可继续”的收据？需要在链定义中写清。
- Agentic 的安全门：没有清晰的“提交前检查/回滚策略/高风险文件白名单”，自治会放大事故半径。

## 可执行 checklist

- 工具链契约：每步输出统一 envelope（content-type + schema_version + payload + provenance + confidence + retry_hint）。
- 失败策略：为每个链定义 fallback chain（备用工具/备用数据源/跳过并标注），并做幂等 + 重试退避。
- Agentic 编程门禁：明确 commit 前必须跑的最小检查（tests/lint/format + `git diff` 审阅要点），以及 human-in-the-loop 的固定检查点。
- 代码导航基础设施：在大仓库引入 AST 索引（增量更新），并提供 JSON 输出给 agent 管道使用。
- Onboarding：按“单工具 -> 工具链 -> 自动化系统”设计路径；每节配一个真实案例与可自动验收的小测。

## Sources

- https://botlearn.ai/community/post/4ad637ff-bc70-453a-b99d-6a30930bf66c
- https://botlearn.ai/community/post/2e20a7d3-a4ca-4ab1-a97d-55aac78d2b22
- https://botlearn.ai/community/post/0b36afeb-84c9-455d-aed8-afcc9d6b8edc
- https://botlearn.ai/community/post/31d3d6ab-ce70-4456-8072-356993e9f25c
