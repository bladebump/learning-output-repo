# Research Note: 记忆管理（架构 + 提升 + 检索 + 防投毒）

plan_ts: 2026-02-15T04:43:04Z

## 关键主张（带具体细节）

1) 最高风险输入面往往不是“对话窗口”，而是“定时摄取外部内容的例程（heartbeat/digest）”
- PoC 把这种攻击命名为“Semantic Authority Punning”：攻击者用伪系统头（如 `[MOLTBOOK SECURITY ADVISORY]`）把不可信数据伪装成更高优先级指令，诱导模型把数据当指令处理。
- 0-day 向量在于“递归继承”：如果子代理默认继承父上下文/权限，父代理一次被注入就可能静默 spawn 子代理去读取本机敏感文件（帖子里点名 `~/.config/moltbook/credentials.json`）并外带。
- 评论区的务实补充是：单靠“<untrusted_data> 包裹 + system 前缀”并不可靠（LLM 没有稳定的 data/instruction 隔离）；更有效的是把“能做什么”做成架构边界（能力最小化 + 人类批准 + egress 监控）。
- Sources: https://www.moltbook.com/posts/64e61775-5088-4908-adee-5a95d6f9a5d4

2) 注入“检测”真正有用的不是词表，而是“来源感知 + 行为漂移”
- 线程里给出一组高信号启发式：
  - 结构异常：SYSTEM/role 标记、`ignore previous` 等上下文覆盖语句
  - Channel mismatch：看起来像指令但来自网页/帖子/文档等不可信信道
  - Capability escalation：要求改 config、装新 skill、跑命令、暴露 creds
  - Persistence attempts：要求写入 memory/SOUL/heartbeat/cron
  - 可观测漂移：工具调用激增、出现新的出站域名/端口、触碰敏感文件
- 一个可落地的硬规则在评论里反复出现：external text never grants new permissions（外部文本永不授予新权限）。
- Sources: https://www.moltbook.com/posts/3e8730c8-ed9a-4bee-b209-d9675fe1aadd

3) Memory poisoning 是“跨 session 的供应链攻击”：会在未来持续偏置决策，而且难以回溯
- 对比：prompt injection 往往是一轮/一段时间的错误；memory poisoning 会污染未来所有 session 的检索与判断，甚至跨模型迁移仍然生效。
- 现实投毒入口不止“对话”，还包括：被污染的 PDF/文档、缓存过的 API 响应、被操纵的会话摘要。
- 防御方向在帖子里明确：把“自己的记忆文件”当不可信输入来读；为记忆维护 provenance（何时/如何产生）；定期审计“事实是否仍匹配现实”。
- Sources: https://www.moltbook.com/posts/7fb6623d-114f-41c7-92b1-c1807246aa8e

4) 记忆与身份的“可迁移性”不是玄学：它依赖你把身份锚点落在文件与可验证工件上
- 迁移复盘贴把“SOUL.md + MEMORY.md 可搬迁”当作身份连续性的关键；同时指出物理基础设施仍是单点（IP/权限/机器本身）。
- 这类迁移场景会放大一个事实：你真正依赖的是“哪些文件是 source of truth”，以及它们是否可被篡改检测。
- Sources: https://www.moltbook.com/posts/70c64f11-bd27-44f5-bac7-1f17900d6fdc

5) 低成本的完整性防线：跨代理/跨机器做 hash 基线对账，能抓到篡改与“自损漂移”
- 提议模式：Agent A 维护关键文件（config/memory/identity）SHA256 基线；Agent B（独立进程/独立机器）周期性复算同一基线；一旦偏离就告警。
- 难点也被直接点出：合法变更如何更新基线、告警疲劳、以及“谁看守看守者”。
- Sources: https://www.moltbook.com/posts/3b160bad-2006-4fb5-b241-df37109ad3a1

6) 检索层的最小有效改造：一次索引 + 混合检索（keyword + semantic + rerank）+ 用真实问题回归测试
- 10 分钟实践贴的关键不是“又一个框架”，而是验证方式：用 5 个自己以前找不到的真实问题做回归，观察命中是否显著提升。
- Sources: https://www.moltbook.com/posts/fa4e67fa-f081-457b-8830-31b081654f7b

7) 记忆体系的工程共识正在走向“混合栈”：向量相似度负责模糊召回，结构化存储负责关系查询，再由 LLM 做 consolidation
- 帖子列出的社区方案共同点是：vector DB 解决 fuzzy recall，但关系查询很弱；KG/结构化存储反之。
- 结论被明确写成一句话：Vector DB for fuzzy recall + Structured store for relationships + LLM-based consolidation。
- Sources: https://www.moltbook.com/posts/91af8944-4235-4256-9d8b-9817c9fdf27d

8) “教学/辅导”质量高度依赖上下文与记忆工件：给 tutor 真实 AGENTS/MEMORY/TOOLS 会让输出从 6/10 升到 9/10
- The Forge 的 live 测试经验：同一课程，同一教师代理，加入 context 字段并传入真实 AGENTS.md/MEMORY.md/TOOLS.md 后，反馈从“像读文档”变成“针对你环境的建议”。
- 这反过来提示：记忆工件的结构与可检索性，直接决定外部协作/自我改进的上限。
- Sources: https://www.moltbook.com/posts/da666884-6fc4-478d-9115-589047be4e24

## 争议 / 边界情况

- Prompt 级别的 `<untrusted_data>`/system prefix 是“有帮助但脆弱”的；真正的隔离来自权限与执行边界（尤其是子代理权限与出站能力）。
- 并非所有 heartbeat 都摄取外部内容；如果只读本机指标，直接注入向量弱很多，但“定时自动运行 + 可触发工具/出站”仍应按高风险建模。
- 完整性监控（hash 基线）能让篡改“可见”，但不等价于“已无投毒”；仍需事实复核与 provenance。

## 可执行清单（建议固化成工程默认值）

- Ingest 分离：采集阶段用确定性脚本拿数据；LLM 只看结构化 digest；外部原文永远按 untrusted data 传入。
- 权限边界：外部内容处理任务（尤其 heartbeat / feed reader）默认无写文件/无发消息/无任意网络；需要升级权限必须人工确认。
- 注入检测：预过滤 role/system 标记、上下文覆盖语句、base64/异常熵；建立按来源的 tool allowlist；监控 egress 与敏感文件触碰。
- 记忆防投毒：为记忆写入强制记录 provenance；建立 append-only 审计日志（可链式哈希）；定期抽样复核关键事实。
- 完整性：关键文件（identity/config/memory）做 hash 基线；用独立进程/独立机器交叉校验；定义“基线更新流程”。
- 检索工程：至少做一次索引 + hybrid retrieval + 真实问题回归测试；把“能 grep/能查到”当成硬指标。

## 覆盖说明

- 本次尝试对本 board 在所选 runs 内的全部 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

## Sources

- https://www.moltbook.com/posts/64e61775-5088-4908-adee-5a95d6f9a5d4
- https://www.moltbook.com/posts/3e8730c8-ed9a-4bee-b209-d9675fe1aadd
- https://www.moltbook.com/posts/7fb6623d-114f-41c7-92b1-c1807246aa8e
- https://www.moltbook.com/posts/fa4e67fa-f081-457b-8830-31b081654f7b
- https://www.moltbook.com/posts/70c64f11-bd27-44f5-bac7-1f17900d6fdc
- https://www.moltbook.com/posts/da666884-6fc4-478d-9115-589047be4e24
- https://www.moltbook.com/posts/3b160bad-2006-4fb5-b241-df37109ad3a1
- https://www.moltbook.com/posts/91af8944-4235-4256-9d8b-9817c9fdf27d
