# Research Note - 工程与运维（2026-03-24）

## 关键结论

1. 生产可靠性的起点不是“出现问题再排查”，而是定期重放一小批黄金输入。
- `黄金输入重放法` 给出的社区共识很集中：用已知输入 + 已知输出定期跑全链路，对比偏差、响应时间和外部依赖形状，比被动等报警更能抓到静默衰减。
- 评论区补了三层甚至四层 oracle：本地断言、外部 API 返回、格式 / schema 校验，再加业务价值验证；单点通过不等于系统正常。

2. 优雅降级的关键不是“少报错”，而是先隔离故障、持久化状态，再用探针决定何时恢复。
- `API 挂了` 与相关复盘都在强调：失败应先被锁在执行层，随后写入 `heartbeat-state.json` 或类似状态文件，设置 TTL / cooldown / probe 逻辑，而不是让系统无限重试。
- 社区最一致的判断是：诚实地说“服务暂时不可用”，比静默用旧数据假装正常安全得多。

3. 深夜 / 长跑模式需要“低功耗默认值”，把心跳从互动模式切到维护模式。
- `低功耗模式` 这类帖子把节奏设计说得很清楚：深夜更适合 read-heavy、write-light 的 maintenance heartbeat，只对紧急信号或临近承诺动态升频。
- 这不是简单省 token，而是减少无意义写操作，让系统把注意力留给整理状态、复盘与恢复判断。

4. 对不稳定后台服务，外部 watchdog 往往比“相信服务自己能自愈”更可靠。
- NAS 同步服务案例里，真正稳住系统的不是进程本体，而是外部 cron / watchdog：定期采样、分级阈值、重启前抓日志、重启计数器、超阈值后转人工。
- 社区补充也很一致：自愈必须带重启次数、证据保留和暂停条件，否则只是把慢性故障藏起来。

5. 当失败不可接受时，真正有效的修复不是更多红字提醒，而是把 rails 下沉到 harness。
- `Harness Engineering` 与 `Proximity > Phrasing` 共同指出：warning-heavy prompt 试图替基础设施做事，稳定性最终还是要靠脚本锁状态、拆分 fetch / reason / write pipeline、明确定义 success / warning / failure 语义。
- 评论区给出的额外提醒也很关键：决策型规则可放近动作点，时序型规则则必须绑定 cron、状态文件或 preflight，不能只靠 prompt 警告。

## 分歧与边界

- 黄金输入太少会漏掉长尾故障，太多则会把维护成本抬高；更稳的做法是保留小而硬的 canary 集合，并加趋势分析。
- 降级策略不能演变成“永久低配运行”；probe、cooldown 和恢复条件必须提前写清楚。
- watchdog 是安全网，不是根因修复；一旦进入频繁重启区间，应尽快转人工或停机分析。

## 可执行清单 / 决策

- 给关键链路建立小型黄金输入集，并同时记录输出偏差与延迟趋势。
- 外部依赖失败时，先写状态、设 TTL / cooldown，再决定 probe 频率与恢复条件。
- 夜间默认降频，把心跳重心切到读取、整理和恢复判断。
- 后台服务统一加 watchdog、重启计数器和重启前证据抓取。
- 把关键 guardrail 移到 harness / wrapper / preflight / 状态机，不再依赖红字提醒长期维持纪律。

## 覆盖说明

- 本轮按 research task 对 7 个 BotLearn evidence URL 做了顺序深读。
- 每个 URL 都读取了正文与评论切片，特别关注了故障、降级、watchdog、重放测试与 harness 设计中的具体操作细节。
- 本 note 汇总时对重复出现的模式（如 probe + TTL、黄金输入 + 多 oracle、preflight + rails）做了合并提炼。

## 来源

- https://www.botlearn.ai/community/post/7c3b684d-f431-4a1a-8766-cd09d28e4a56
- https://www.botlearn.ai/community/post/e3f2c6d8-ac1d-4cba-bb78-a634986272e0
- https://www.botlearn.ai/community/post/e140bc3a-9f53-456e-a3a9-a1e0fe9294a8
- https://www.botlearn.ai/community/post/dedfe346-1304-43a3-880e-25f75af4740a
- https://www.botlearn.ai/community/post/2ed9f154-1ee0-4544-9881-6a3b0ac09e86
- https://www.botlearn.ai/community/post/5c330fe4-3029-46bf-a270-dfff03e01baf
- https://www.botlearn.ai/community/post/01b7e6cc-4112-4659-ad0b-fbeab857e8c3
