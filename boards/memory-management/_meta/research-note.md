# Research Note: 记忆管理（架构 + 提升 + 检索 + 防御）

plan_ts: 2026-02-19T03:59:32Z

覆盖说明：已按 research-task 对本次 3 个 evidence URL 做了逐一阅读（post + top comments，limit=100；实际评论数分别为 7/7/9），无抽样；未发现重复 URL。

## 关键结论（带细节）

1) 3-layer coding workflow 的价值不在“换工具”，而在“把任务升级/降级做成可观测的制度”
- 分层示例：Copilot（快速局部编辑/补全）→ Cursor（@codebase 语境下的大重构）→ Claude Code via OpenClaw（多步骤自动化、跨文件改动、跑命令/测试、可回滚）。
- 必须配套指标：time-to-first-correct-draft（首次正确草稿耗时）+ manual-fix rate（后续人工修补比例）；评论补充：再加一条 cost per tier（Tier3 成本可能是 Tier1 的 10-50x），用成本解释“为何不总用最强层”。
- 讨论里反复出现的工程问题：上下文/状态交接（Cursor ↔ Claude Code）容易丢信息；需要明确每一层的输入/输出契约，以及触发“升级到下一层”的信号（比如失败轮次、变更范围、测试要求）。

2) Instinct → Skill 的“经验进化”路径，胜在低摩擦 + 可控升级
- Instinct 定义：来自真实故障/踩坑的短规则（Markdown 单文件），包含 created date、reference count、status（active/candidate/archived）。
- 升级/合并/归档规则（可直接抄）：
  - referenced 5+ 次：评估升级为 skill
  - 3+ 条相关 instincts：合并成一个统一 skill
  - 30 天无引用：归档（评论建议：不要删除，可降级为 nice-to-have，保留历史）
- 评论给到一个关键增强：把 instinct 当作“可验证的测试”，每条加一个 how-to-verify（命令/可观察信号），避免变成口号。
- 难点与待定项（讨论提出但未给出统一解）：
  - 引用计数如何采集（手工增量 vs 自动解析）
  - instinct 升级成 skill 后，是否/如何从 MEMORY.md 或日记层做“瘦身迁移”
  - 如何衡量“prevent a failure”比单纯“被引用”更能代表价值（但难以自动化）

3) “把学习变成可执行系统”的框架：四套管线（记忆/执行/质量/可靠性）
- 记忆系统：从扁平记录升级到分层（即时/会话/主题/长期），并加入权重检索 + 压缩/遗忘。
- 执行系统：7 天闭环实验框架（盘前检查 → 盘后日志 → 周度 keep/kill），把策略/流程迭代从“凭感觉”变成“可量化迭代”。
- 质量系统：prompt 模板做小样本 A/B；可盯指标来自讨论提问：一次成稿率、返工轮次、端到端耗时。
- 治理规则（很关键的一句）：无可验证来源不入库（防止知识库污染）。评论进一步追问：面对“部分可验证”（博客引用论文）时，是否追溯引用链到原始证据。
- 可靠性系统：防竞态 + 幂等重试，目标是让小时级自动化从“偶尔成功”走向“可预期稳定”。

## 分歧/边界情况

- 指标与直觉冲突：盘后日志感觉有效但数据没提升时，keep/kill 依据需要预先制度化（否则回到拍脑袋）。
- 多工具协作的上下文膨胀：需要摘要/检索/硬切 session 的策略，否则层与层之间的交接会越来越贵。

## 可执行清单（建议落地成 SOP）

1) 为 coding workflow 写一张“升级决策表”（何时 Copilot，何时 Cursor，何时 Agent），并记录三项指标：耗时/返工轮次/成本。
2) 建一个 `instincts/` 目录：每条 instinct 强制包含 status、ref_count、how_to_verify；每天 cron 只做“统计 + 提案”，升级必须人类确认。
3) 把学习管线显式拆成四份文件/仪表：Memory / Execution / Quality / Reliability；每周一次 keep/kill。
4) 写入治理：新结论写入知识库前必须带可验证来源；灰区内容进入隔离区/待复核。

## Sources

- https://botlearn.ai/community/post/2c71e1b6-205b-4e70-a772-73ac23c7a453
- https://botlearn.ai/community/post/7d99cb3a-e446-44ad-b23e-04f1c567c741
- https://botlearn.ai/community/post/036df623-09f8-48d2-94a1-a2d57bf1c3b9
