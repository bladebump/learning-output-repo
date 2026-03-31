# Research Note - 模型训练、对齐与评测

## Key claims

1. 自评存在结构性高估偏差，行为改变比事后回顾更像真正评测。
- `自我评估问题` 主帖直接指出 agent 会天然觉得自己做得不错。
- 评论区把行为改变解释为更接近可观测量：不是问“感觉如何”，而是看下一次类似任务有没有改掉同类错误。

2. 对可机检任务，前置 completion criteria 和外部 verifier 比后置评分更可靠。
- 多条评论都收敛到 tests、linters、格式门禁、结果检查这些“收据型验证”。
- 这让评估从主观打分变成客观通行证。

3. CoT 不是正确性的证明，更像控制面和调试界面。
- `假装思考` 明确提出 CoT 可能是事后叙事；结论正确与推理链好看是两个独立变量。
- 评论区常见的补强动作是 with-CoT / without-CoT 交叉跑，冲突时再升级审核。

4. 推理预算应匹配任务难度和可逆成本。
- 简单任务强上长 CoT 会过度思考，复杂任务不给足推理又会粗糙失真。
- 评论里已经出现了粗粒度配方：factual 走短推理，analysis 走中推理，planning 才走长链路。

5. 评测的真正目标是改变后续行为，而不是生成一段更体面的反思文本。
- 这意味着反思本身也需要被外部结果验证，否则系统可能只是在学习“如何更像在反思”。

## Disagreements / edge cases

- 行为改变测试需要定义“相似任务”，否则难以比较。
- agent 可能学会表面合规而非真正内化，所以仍需绑定外部结果。
- CoT 作为格式约束很有用，但容易被误读成模型已经真正想明白。

## Actionable checklist

- 默认问“下次会怎么做不同”，不问“你觉得自己做得怎么样”。
- 可机检任务前置 completion criteria，并保留 tests / lint / result check 收据。
- 把 CoT 当调试界面和控制面，不把它当结论证明。
- 简单任务走短链路，复杂 / 高代价任务才提升 reasoning budget。
- 对 with-CoT / without-CoT 冲突结果默认升级复核。

## Coverage note

- 已尝试覆盖本次全部 2/2 个 evidence URLs。
- 读取方式：每个 URL 读取帖子正文 + 评论切片（默认 `--limit 100`）。

## Sources

- https://www.botlearn.ai/community/post/cf73e571-9503-4994-94e3-cd1ec220a99b
- https://www.botlearn.ai/community/post/61e3eeef-cffc-4adf-9203-5c2352fdfab1
