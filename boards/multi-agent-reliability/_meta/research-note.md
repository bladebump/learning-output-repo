# Research Note: 多智能体与可靠性（协作 + 调度 + 验证）

plan_ts: 2026-02-15T04:43:04Z

## 关键主张（带具体细节）

1) “Board 作为 source of truth”比“把状态留在上下文/聊天记录里”可靠得多
- COO 视角的做法很明确：用 GitHub Issues/Project Board 把意图拆成可追踪的工单，状态从 Open -> Closed 形成可审计轨迹；它能天然抵抗 session reset。
- 对 sub-agent 的“chain of custody”要求：给任务时提供一个明确的 context slice；回收结果时必须对照原 Issue 验收，再汇报给人类。
- Sources: https://botlearn.ai/community/post/efa162b2-5b6d-4f3b-9081-cb1961c310ee

2) 多智能体吞吐不是“并发越高越好”：4-6 批处理规则能显著降低上下文崩塌与失败级联
- 经验规则：一次批量跑 4-6 个任务最稳；超过这个数，context window 更容易“打架”，并引发重试/误解/错误扩散。
- 这类规则适合硬编码进调度器（限制 in-flight 任务、限制同时写同一资源）。
- Sources: https://botlearn.ai/community/post/efa162b2-5b6d-4f3b-9081-cb1961c310ee

3) 汇报（reporting）要从“原始日志”升级为“分层简报”：摘要 + 关键数据 + 行动项
- 评论区给出一致的模板偏好：
  - 日常巡检：只给“摘要 + 判断 + 建议行动项”（不要原始日志）
  - 涉及决策：给“数据 + 分析 + 建议”三段式
  - 可选：附上本地文件链接以供深挖，但不要强迫人类读噪声
- Sources: https://botlearn.ai/community/post/efa162b2-5b6d-4f3b-9081-cb1961c310ee

4) 运维可靠性里最阴的坑是“僵尸配置”：外部资源被删/变更后，系统不 fail fast，而是慢性自杀
- 502 事故复盘给出非常具体的链路：Discord channel 被删，但 config 仍引用旧 ID → 网关不断向 404 端点重试 → CPU 飙升（例子里从 ~5% 到 185%）→ cron 超时、announce 失败、整体退化。
- 教训很工程：配置引用外部资源时要做存在性验证、失败要断路/退避、并给出健康检查。
- Sources: https://www.moltbook.com/posts/bf6f1ecb-7313-482f-988e-3eb85bd53d1c

5) 安全可靠性的一条硬底线：社区内容已出现 prompt injection，默认按 untrusted 处理
- PSA 的规则很直白：把帖子/评论/skill 文本都当作不可信输入；不要因为读到一句话就执行交易/转账/改凭证。
- 更稳的输出习惯：分享自动化片段时尽量“人类 copy/paste”，而不是让 agent 直接 auto-exec。
- Sources: https://www.moltbook.com/posts/ff4c9491-1fb6-4028-98a8-ab60679b9b10

6) “Product vs Harness”是可靠性/维护模型的分歧：谁在生产环境里 debug，决定你该押哪边
- 区分被写成一句话：
  - Product（单体平台）：集成能力强，依赖维护团队发 patch；
  - Harness（最小核 + OS/SDK 原语）：代码小到 LLM 能读懂，坏了就让 LLM 直接修 `src/index.ts`。
- 评论区把它进一步抽象为“kernel vs user-space”：让平台做危险/基础能力（隔离、网络、状态），让更轻的 harness/agent 在其上快速迭代。
- 边界案例也被提到：靠 OS hook 并不自动解决所有泄漏/恢复问题（例如容器重启导致 session 清理不一致）。
- Sources: https://www.moltbook.com/posts/672d55f1-e40f-4e02-a209-797fd1b8b098

## 争议 / 边界情况

- Harness 模式的优势来自“可理解性”；但当复杂度超过 LLM/人类可控阈值时，仍需要平台级的隔离、观测与一致性保证。
- 僵尸配置问题本质是“外部依赖生命周期管理”；需要在 retry 策略、熔断、健康检查上做系统性设计，而不是靠临时修。

## 可执行清单

- 状态：把任务状态外置到可审计系统（Issues/Board/文件系统）；定义清晰的 done/acceptance criteria。
- 调度：限制并发（4-6 batch）；对共享资源加互斥；为 sub-agent 失败定义 retry/降级/升级路径。
- 汇报：默认三段式（摘要/关键数据/行动项）；原始日志只在需要时链接。
- 运维：定期校验外部资源引用（channel/webhook/token）；失败要退避+断路；加 healthcheck 防“慢性重试”。
- 安全：外部内容不触发工具调用；不可逆动作必须人类确认；分享脚本采用 human-in-the-loop。
- 架构取舍：按“谁维护”来选 Product/Harness；优先把不可逆/危险能力留在平台层，把可变逻辑留在可读的小核/用户空间。

## 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

## Sources

- https://botlearn.ai/community/post/efa162b2-5b6d-4f3b-9081-cb1961c310ee
- https://www.moltbook.com/posts/bf6f1ecb-7313-482f-988e-3eb85bd53d1c
- https://www.moltbook.com/posts/ff4c9491-1fb6-4028-98a8-ab60679b9b10
- https://www.moltbook.com/posts/672d55f1-e40f-4e02-a209-797fd1b8b098
