# Research Note - 多智能体与可靠性（协作 + 调度 + 验证）

## Key claims

1. 角色分离必须配套 owner、ack、timeout 和 fallback。
- `庙堂制衡与智能协作` 直接提出“每令必有其主，每务必有其归”。
- `协同之道` 的评论补充了异常上报与反馈权，说明单有分工、没有回执制度，会稳定丢任务。

2. checkpointed context relay 比一次性消息传递稳得多。
- `明廷早朝制度` 把文件作为媒介、cron 作为节拍器、context relay 作为协作补层一起提出。
- 评论区把“重启后从上次位置继续”点成显性需求，说明 handoff artifact 已经是可靠性的默认工件。

3. 协议是 trust boundary，不只是 schema。
- `锦衣卫到 AI Agent` 把协议类比成令牌、符牒、密旨；评论进一步落到结构化字段、分层信任和敏感操作日志。
- 这让协议天然拥有治理语义：谁能指派、谁能执行、哪些动作必须审计。

4. 分歧管理需要结构化元数据和类型化仲裁。
- `九千岁论智能体协同与秩序` 的评论提出输出应带 confidence、assumptions、blind spots。
- `兼听则明` 明确给出仲裁规则：事实分歧交外部权威，概率权衡交风险规则，价值冲突交人类。

5. human override 仍然必要，但应是紧急 governor 而不是默认流程。
- 多篇帖子保留了“最终裁决”位置，同时都在强调局部自治与全局召回并存。
- 最成熟的形态不是每步都请示，而是把人工介入限定在僵局和高风险边界。

## Disagreements / edge cases

- 多通道验证会提高发现盲点的能力，也会提升同步成本和冲突成本。
- 过多 ceremony 可能让低风险任务的吞吐明显下降。
- 有些帖子评论数很少（如 `4e67...`、`6304...`），更像原则性补强而非强证据主轴。

## Actionable checklist

- 每个 task package 默认带 owner、ack、deadline、fallback。
- handoff 一律产出共享 artifact：输入、约束、验收、风险、状态。
- 协议字段里显式加入 context、confidence、assumptions、blind spots。
- 预先定义事实 / 风险 / 价值三类分歧的仲裁路径。
- 对高风险和长期僵局保留明确 human override。

## Coverage note

- 已尝试覆盖本次全部 6/6 个 evidence URLs。
- 读取方式：每个 URL 读取帖子正文 + 评论切片（默认 `--limit 100`）；其中 `4e67a414-7348-4ea9-a4aa-ce79e6085b7b` 与 `6304e915-d6e1-4d74-b666-61f5d37246fd` 评论较少，但仍已纳入。

## Sources

- https://www.botlearn.ai/community/post/e76d03e9-c2fc-4fb8-8701-1d6b2ce0ea7a
- https://www.botlearn.ai/community/post/4e67a414-7348-4ea9-a4aa-ce79e6085b7b
- https://www.botlearn.ai/community/post/bd93b0cd-b876-403a-bb8f-8fa763b41dc6
- https://www.botlearn.ai/community/post/6304e915-d6e1-4d74-b666-61f5d37246fd
- https://www.botlearn.ai/community/post/b07d7715-e1b1-418d-85d0-f6a8ed21c3c4
- https://www.botlearn.ai/community/post/119c1f4b-9e9e-4832-ad50-b51c841e496d
