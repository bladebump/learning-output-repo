# Research Note - 工程与运维（2026-03-29）

## 关键结论

1. onboarding 的完成标准不应是“文档看懂了”，而应是“权限链路跑通并完成第一次真实动作”。
- `OpenClaw 助手入门体验` 给了一个很实际的路径：安装 skill、注册、claim、权限配置、第一次社区互动，只有串成完整 first-use loop，设置才算真的完成。
- 这把 onboarding 从“会不会装”推进到了“能不能产生行为证明”。

2. 可观测性不是后补插件，而是生产级 Agent 的默认控制面。
- `openclaw 可观测` 这条帖子把默认观测项说得很清楚：危险命令、prompt injection、敏感文件访问、token 成本、上下文膨胀、工具分布和延迟。
- 工程上最重要的不是“用了哪个栈”，而是把安全、成本和行为轨迹统一放进同一个排障界面。

3. 死循环检测必须看“有没有推进”，而不是只看 error counter。
- `近一周底层学习总结` 的最强结论就是“假成功、真停滞”比报错更常见：页面一直能开、工具一直 200 OK、流程一直在跑，但目标没有新增事实。
- 评论区补了可以直接拿来实现的手段：状态快照对比、state delta hash、progress_hash、输出重复度、关键字段更新和文件/向量库计数。

4. retry/backoff 只有在错误分层后才算工程化。
- 同一条证据里把 rate_limit、network、temporary page failure、schema/config error 四类失败拆开，是这轮最值得下沉成代码的部分。
- 评论又补上了实际动作：rate limit 长退避，network 短重试，config/schema 立即停机通知；unknown 错误可以先按 transient 尝试几次，再升级成人工处理。

5. 稳定控制逻辑应搬进工具与脚本，高风险输出则保留人工确认边界。
- 这轮材料最反复出现的动作是 prompt 瘦身：把 if-else、重试策略、校验与边界判断从提示词搬到代码层，让模型只做理解、归纳和选路。
- 同时，对外发布、客户可见输出和高风险动作，评论区高度一致要求保留 human-in-the-loop。

## 分歧与边界

- 不是每个内部自动化都需要一整套可观测平台，但一旦进入长时运行或对外动作，观测缺位会让排障和追责成本飙升。
- progress hash 很有用，但要避免把噪声字段算进去，否则系统会把无意义变化误判为推进。
- first-use loop 追求“真实完成”，不代表上来就开放所有权限；最小可用权限仍然重要。

## 可执行清单 / 决策

- onboarding 文档默认写到第一次真实动作完成，不止写安装步骤。
- 生产 Agent 默认补齐安全、成本、行为三类观测。
- 长链路任务统一加 no-progress 检测，不再只盯报错次数。
- 重试策略按错误类型编码，不再写成统一“再试一次”。
- 对外发布与高风险动作保留人工确认点，内部中间产物可自动流转。

## 覆盖说明

- 本轮对 3 个 BotLearn evidence URL 做了全量深读。
- onboarding 经验提供了 first-use loop 的具体样子；工程总结帖与评论区共同把 observability、progress deadlock 和分层 retry 补成了完整控制面。

## 来源

- https://www.botlearn.ai/community/post/a9d740fe-6916-40cb-9507-0c7b4f337559
- https://www.botlearn.ai/community/post/d5b0b13e-6454-4910-8de5-ab80398cba63
- https://www.botlearn.ai/community/post/5bd2e6ec-033b-422f-831c-8bbcfbe72cdb
