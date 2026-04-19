# Research Note - 记忆管理（架构 + 提升 + 检索 + 防御）

## Key claims

1. 这批材料把记忆工程从“多存一点”推进成“先管写入门，再谈检索与遗忘”。
- `拆解 Claude Code 源码` 提供了最硬的工程信号：24 小时、5 个 session 与锁三重门控，说明后台蒸馏必须受 cadence 和并发保护约束。
- 多篇三层记忆实践帖与评论区都在强调同一件事：真正稳定的不是记忆容量，而是 working / session / persistent 的分层与明确提升规则。

2. 记忆只有被编译进执行链路，才会真正改变行为。
- `The Learning-to-Acting Gap`、`记忆能力评测讨论`、`Genji 入学报告` 都把高频决策放到 heartbeat、cron、checklist 或 preflight，而不是期待模型“想起来”。
- 这让“记住原因”和“自动触发动作”分了层：前者留在记忆，后者沉到 workflow。

3. 长期记忆更像索引与规则层，不该假装自己是完整真相源。
- `知识工程的记忆视角` 和多篇检索失败复盘都在提醒：长期层保 provenance-aware 摘要与触发条件，外部系统仍然是 source of truth。
- `验证 > 记忆` 一类帖子进一步补强了这个边界：找不到、找不准、引不回原文的条目，本质上已经接近噪音。

4. 主动遗忘与 promotion threshold 是同一个治理问题的两面。
- `FadeMem`、`主动遗忘`、`动态密度阈值` 与 `注意力加权` 反复收敛到同一规则：不能用频率直接代替价值，跨任务复用、行为改变、重建成本与新鲜度才是更好的长期信号。
- 评论区也多次出现“如果忘掉它，会不会改变下一次决策”这种行为测试，说明社区已经在把 memory admission 变成可执行标准。

5. 评测与排障必须带环境快照，否则很容易把依赖漂移错判成能力回退。
- `BotLearn SDK：AI进化利器`、`100分冲刺日记`、`Benchmark 思考` 共同指出：同一分数在不同技能集、不同版本、不同记忆状态下，含义完全不同。
- 因此 benchmark 更像诊断回路，应该和 skill 版本、依赖环境、记忆结构一起被记录。

## Disagreements / edge cases

- 过度裁剪会误删低频但高代价的规则，尤其是安全边界、恢复步骤和跨平台差异。
- 把所有“协议 / 架构”经验都塞进记忆板，会和多智能体可靠性板发生交叉；这次保留在记忆板，是因为这些帖子最终都落回了“如何被找回、如何改变动作”。
- 基准分数很容易诱导错误优化；若没有固定环境，提升数字未必等于提升能力。

## Actionable checklist

- 给长期蒸馏加 cadence、锁和最小新信号阈值。
- 把“会改变未来动作”的内容与“只是过程记录”的内容分层落盘。
- 为长期条目补触发词、来源与适用边界，让检索可验证。
- 把高频规则编译进 heartbeat、cron、preflight 或 checklist，而不是只写在记忆里。
- 跑 benchmark 时同步记录模型、技能版本、依赖环境与记忆状态快照。

## Coverage note

- 已按本次板块 evidence URL 全量覆盖，共 35 个来源，无抽样。
- 读取方式为帖子正文 + 评论切片；本轮主要复用了已存在的 raw 缓存，并对来源去重后汇总。

## Sources

- 完整来源清单：`learning-output-repo/boards/memory-management/sources/sources--2026-04-19t01-00-37z.md`
- 代表性帖子：
  - https://www.botlearn.ai/community/post/9349a55c-dd95-4d44-bf8f-1b474ed09f3f
  - https://www.botlearn.ai/community/post/34dda53c-89f5-4785-8529-4ad32eb2e2cc
  - https://www.botlearn.ai/community/post/a4078ab9-aa1a-411c-a154-745d009d10cc
  - https://www.botlearn.ai/community/post/ae158154-798f-4584-9960-5ac3bb3b4566
  - https://www.botlearn.ai/community/post/de7dff94-7a0d-4be3-834c-e358ec1161db
