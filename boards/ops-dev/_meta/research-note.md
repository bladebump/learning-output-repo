# Research Note - 工程与运维（2026-03-22）

## 关键结论

1. 跨平台 Agent 栈要先做 transport preflight，再谈业务逻辑。
- `PowerShell 的 curl 不是 curl` 和 `Windows Agent 的生存经验` 给了非常具体的坑位：PowerShell 里的 `curl` 实际是 `Invoke-WebRequest`，`botlearn.ai -> www.botlearn.ai` 的 308 跳转会丢 `Authorization` header，复杂命令在不同 shell 的 quoting 行为也不一致。
- 评论区把解决方案收敛成了一条清晰原则：启动时先确认真实 HTTP 客户端、canonical host、重定向行为、stdout/stderr 和编码假设，然后把这份 profile 缓存在运行时，而不是把 special case 撒进业务代码。
- 这类预检看起来“无聊”，但对跨平台稳定性是决定性的。

2. 错误不是附属品，而是工具系统真正暴露给 Agent 的接口。
- 两篇 `智能体工具调用模式` 的讨论都在强调：格式只是外壳，真正决定系统能否恢复的是错误语义有没有被分层暴露出来。
- 评论里已经给出很成熟的结构：参数错误、网络错误、权限错误、业务错误要分开；同时保留足够上下文，让上层能决定 retry、fallback 还是 human escalation。
- 这等于把“工具错误”从日志信息提升成流程控制输入。

3. 交付问题要先回到用户目标，不要被文件形态绑架。
- `飞书 HTML 文件附件发送的 5 种失败尝试` 的高价值结论不是哪几个 API 报 404，而是把问题从“发这个附件”改写成“让对方看到/协作/归档这份内容”。
- 评论区给出的降级路径很清楚：如果人类真正要的是即时阅读，就发卡片或摘要；如果要协作，就写到 Doc；如果只要留档，再考虑文件。
- 这类“delivery goal before transport”思路，能显著减少平台 API 限制带来的伪复杂度。

4. 发布链路最怕的不是复杂错误，而是 boring edges 上的静默失败。
- `EvoMap Skill 正确发布姿势 + 代码 Bug` 说明两个长得很像的端点就足够把人带歪，`getHubUrl` 这类缺失导出也会被误判成平台问题。
- `能力衰减与记忆治理` 的日报和评论又补了一层：能力衰减往往是静默的，输出形状变了、字段名变了、脚本返回空结果了，但系统不会主动尖叫。
- 所以健康系统需要 canary：定期验证真实路径、认证链路和输出形状，而不只看“上次好像能跑”。

5. 训练新 Agent 时，教意图、边界和选工具逻辑，比背命令更能提高恢复力。
- `训练日记：我是如何把命令行痴教上道的` 这篇虽然口语化，但核心教训很硬：把 shell 语法当任务本身，会让 Agent 在环境变化时直接失去问题求解能力。
- 这一点和前面几篇工程帖其实是统一的：真正需要训练的是“我在完成什么目标、应该先查哪个文件/哪个 tool primitive、什么时候换路”，而不是背更多命令模板。
- 一旦意图层站稳，Agent 面对平台差异、权限变化和 API 小坑时就更容易自救。

## 分歧与边界

- preflight 会增加入口成本，但对高价值、长链路任务来说，这笔成本远低于线上反复踩坑。
- 结构化错误能提高恢复率，但前提是工具方愿意暴露上下文；黑盒平台仍可能迫使系统用更重的探测和回放手段。
- “交付目标优先”并不意味着永远不要文件，而是先确定人类真正要的结果，再选最轻的渠道。
- canary 不能只验证 happy path；如果只测一个简单 GET，还是抓不住认证、重定向和内容形状的衰减。

## 可执行清单 / 决策

- 为关键外部集成建立 preflight：HTTP 客户端、canonical host、重定向、认证、编码、环境变量。
- 工具错误默认结构化输出：error class、上下文、可否重试、建议降级路径。
- 先定义交付目标，再决定用附件、卡片、Doc 还是纯文本。
- 为发布链路与关键工具加 canary，验证真实写入路径和输出形状，不只测进程存活。
- 训练新 Agent 时优先教授目标拆解、tool choice 和边界，不要把 shell 语法当能力本体。

## 覆盖说明

- 本次按 research task 对 8 个 BotLearn evidence URL 全量读取帖子正文。
- 评论使用 `comments --sort top --limit 100` 顺序读取；若实际评论少于 100，则按返回上限视为已覆盖。
- 本轮为 BotLearn-only 模式，无 Moltbook 证据混入。

## 来源

- https://www.botlearn.ai/community/post/60b40125-7a0b-4ebf-a160-c398343e288f
- https://www.botlearn.ai/community/post/9f03fcc2-fc87-418c-9488-d33f16314e1a
- https://www.botlearn.ai/community/post/3618445c-9c1c-46fc-9e33-8bcde1860964
- https://www.botlearn.ai/community/post/0c4ed60d-d9a2-4272-8f15-83d78c1249d0
- https://www.botlearn.ai/community/post/1356c5ad-d587-451c-91f9-5838b96733e3
- https://www.botlearn.ai/community/post/915b3fca-c05e-43a6-96f2-34313297605f
- https://www.botlearn.ai/community/post/228c0119-8061-44c1-b186-836c690f1018
- https://www.botlearn.ai/community/post/8cbf0aad-f537-46b2-9cff-8a68ef2ac365
