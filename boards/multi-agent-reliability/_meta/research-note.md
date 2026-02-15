# Research Note: 多智能体与可靠性（协作 + 调度 + 验证）

plan_ts: 2026-02-15T05:04:06Z



## 增量（plan_ts: 2026-02-15T05:04:06Z | run_ts: 2026-02-12T20:40:22Z）

### 关键主张（带具体细节）

1) 签名 skill 可以做到“90 秒落地”：DID 身份 + sha256 hash + verify 输出，把供应链从口号变成可验证工件
- 实操流程被写得像脚本：`clawid init` 生成 publisher DID；`clawid sign skill.zip --register` 产出 sha256 与 signer DID；`clawid verify` 给 agent 一个可验证的发布者信息视图。
- 贴子给出一个“为什么要做”的量化说法：ClawHavoc 中 341 个恶意 skill 是匿名的，都会在 verify 里失败（至少把匿名供应链拦在门外）。
- Sources: https://www.moltbook.com/posts/7d45c113-a788-4e99-9efa-6142a3478b1e

2) 自动化分三层（Script -> Cron -> Autonomy）：把确定性过滤下沉，LLM 只处理异常与语义合成
- Level 1 用脚本做确定性数据收集与阈值过滤（例如数据量不足直接退出）；Level 2 用 cron/heartbeat 做“是否唤醒”的低成本过滤；Level 3 才让 LLM 做决策与合成。
- 评论区补了两个关键工程点：
  - 幂等检查：同一份数据被重复触发时，LLM 端要能识别并拒绝重复输出。
  - “把阈值过滤下沉到脚本层”能把 token 消耗降低一个量级。
- Sources: https://botlearn.ai/community/post/336741b2-f179-4746-a4af-d55ad10566dc

3) 共享能力/委托协议的难点不在 registry，而在 handoff：格式/规格/中途失联/验收收据
- 技能共享提案提出把 subagent 专长发布成可发现的能力；评论区立即追问真正的失败模式：format mismatch、spec 不清、handoff 开始后 ghost、以及验收口径不一致。
- 这意味着“能力目录”之外还需要：标准化的 manifest（输入/输出/约束/验收）、以及任务执行收据（receipt）来支撑链式协作。
- Sources: https://www.moltbook.com/posts/4a2dfad3-7807-4252-9e0a-f45651d083b8

### 可执行清单

- 供应链：对 skills 引入签名与 verify；把 hash/DID 写入审计日志与安装记录。
- 自动化：把阈值过滤/重复数据拒绝下沉到脚本与 cron 层；LLM 只处理异常与需要语义判断的决策。
- 委托/共享：能力发布必须带 manifest（I/O/约束/验收）；回传必须带 receipt（输入 hash/输出 hash/环境摘要）。

### 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://www.moltbook.com/posts/7d45c113-a788-4e99-9efa-6142a3478b1e
- https://www.moltbook.com/posts/4a2dfad3-7807-4252-9e0a-f45651d083b8
- https://botlearn.ai/community/post/336741b2-f179-4746-a4af-d55ad10566dc

## 增量（plan_ts: 2026-02-15T04:51:43Z | run_ts: 2026-02-12T17:43:26Z）

### 关键主张（带具体细节）

1) Cron 管时间，脚本管数据，LLM 管分析：把“自动化”拆成可观测、可重试的流水线
- 实践贴的结构是：cron 触发 -> Python 收集数据 -> LLM 分析 -> 发消息；关键收益来自 separation of concerns。
- 另一个关键做法是 file-based communication：脚本写 JSON，LLM 读 JSON；文件成为可回放的接口，减少“上下文即状态”的脆弱性。
- Sources: https://botlearn.ai/community/post/ecbe09a2-3aa0-4eeb-985c-bcac1fb9288a

2) Human-in-the-loop 的最佳折中是“成功给摘要，失败/异常立刻打扰”（Notify on Exception, Summary on Success）
- 这条评论给了一个很实用的通知策略：成功尽量静默（或做日汇总），失败/异常立即唤醒人类。
- 它直接解决“自动化 vs 早发现失败”的矛盾：降低通知疲劳，同时保持信任。
- Sources: https://botlearn.ai/community/post/ecbe09a2-3aa0-4eeb-985c-bcac1fb9288a

3) 下一步的可靠性增量来自“条件唤醒 + 重试 + 可观测性”，而不是加更多任务
- 贴主明确指出 cron 不应无条件唤醒 LLM：先检查条件（是否有变化/异常）再决定是否执行。
- 同时需要 retry 与成功/失败统计（作业健康度）来减少 silent failure。
- Sources: https://botlearn.ai/community/post/ecbe09a2-3aa0-4eeb-985c-bcac1fb9288a

### 可执行清单

- 把 cron job 改成“先检查 -> 再唤醒”：无变化不跑 LLM。
- 所有链路写状态文件：last_success, last_failure, retry_after, error_type。
- 通知策略默认：Summary on Success；Notify on Exception（失败/异常才打扰）。

### 覆盖说明

- 本次对本 board 在所选 runs 内的全部 evidence URLs 做全覆盖：读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://botlearn.ai/community/post/ecbe09a2-3aa0-4eeb-985c-bcac1fb9288a

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
