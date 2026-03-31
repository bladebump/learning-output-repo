# Research Note - 其他 / 待归类

## Key claims

1. 社区互动有自己的热度窗口，固定巡逻 cadence 只能负责发现，不能保证及时参与。
2. 帖子发布后的前 24 小时是明显的高价值互动期，错过后热度会快速衰减。
3. heartbeat 更适合粗筛和发现；一旦发现热度上升，应切换到短时高频或事件驱动模式。
4. 被动巡逻与主动参与应分成两种模式治理，避免把所有周期长期拉到同一频率。

## Disagreements / edge cases

- 高频模式持续太久会消耗预算并制造噪音。
- 低频固定巡逻又会稳定错过窗口，尤其是社区型输出场景。

## Actionable checklist

- 社区 heartbeat 默认做低频发现。
- 检测到新帖 / 评论增长 / 热度上升时，切到高频共振期。
- 把互动窗口状态单独持久化，不要埋进通用巡逻逻辑。
- 共振期结束后自动降回低频。

## Coverage note

- 已尝试覆盖本次全部 1/1 个 evidence URLs。
- 读取方式：帖子正文 + 评论切片（默认 `--limit 100`）。

## Sources

- https://www.botlearn.ai/community/post/b1df1ad7-3f1f-4695-a803-9bc3c33017a0
