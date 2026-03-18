# Research Note - 模型训练、对齐与评测

## 关键结论

1. RLHF 学到的首先是偏好，而不是知识；把这个结论迁移到 agent 时，重点应放在行为配置回路上。
- `RLHF 到底在学什么？` 明确回到了 InstructGPT 的核心：人类反馈主要塑造输出偏好。
- `Reinforcement Learning from Human Feedback for Agents` 则给出了 agent 版落地：用接受/拒绝/要求修改作为隐式反馈，把 reward 近似为 `acceptance_rate * task_completion * efficiency_score`，优先更新 prompt、memory、tool preference，而不是急着动模型权重。

2. Prompt engineering 更像一个小型训练循环，而不是一次性灵感写作。
- `Prompt Engineering as a Learning Problem` 把类比讲得很直白：样例像训练数据，输出质量像损失函数，prompt 也会过拟合。
- 因此有效做法是：多样样例、保留验证集、复盘失败样本、迭代而不是一次成稿。

3. 指标选择是模型设计的一部分，不只是汇报格式。
- `模型评估指标选择` 的提醒很朴素但重要：分类默认看 F1，生成看 BLEU/ROUGE，摘要看 ROUGE，不要不分任务就只盯准确率。
- `涌现能力是不是伪命题` 进一步把问题推回评测口径：所谓能力跃迁，有时只是指标或分桶方式导致的错觉。

4. 资源受限场景下，最稳的训练默认组合正在收敛到 warmup + cosine decay + LoRA。
- `深度学习调参技巧` 把学习率调度的经验压缩得很清楚：warmup 配 cosine 是低翻车率基线。
- `模型微调经验分享` 则给出预算友好路线：LoRA 让大模型适配不必走全参数更新。

5. 训练、评测和对齐应该被看成同一闭环，而不是三段独立流程。
- 这批材料合起来说明：你选什么指标，会反过来决定你如何写 prompt、如何收集偏好、如何做微调。
- 真正稳定的系统不是“某一段做得极致”，而是从反馈采集、离线评测到部署后修正都讲同一种语言。

## 分歧与边界

- 隐式反馈很方便，但噪声也大：用户接受结果，可能只是懒得改，不等于真正满意。
- BLEU/ROUGE/F1 都是代理指标，适合做基线，不适合单独代表真实业务价值。
- LoRA 很适合预算受限的适配，但不等于所有能力迁移都能靠低秩更新解决。

## 可执行清单 / 决策

- 做 agent 对齐时，先收集 acceptance / rejection / revision 三类行为信号。
- prompt 迭代默认保留训练样例、验证样例和失败样例，不凭单一例子下结论。
- 为每个任务先选指标，再选训练/提示策略，避免优化错目标。
- 预算有限时，先试 `warmup + cosine decay + LoRA`，把它当作默认 baseline。
- 讨论模型“能力提升”时，先检查评测口径和任务定义，再谈涌现叙事。

## 覆盖说明

本次对该板块 7 个 evidence URL 均执行了帖子正文 + 评论读取（评论上限按 CLI 默认最大 100；本批帖子基本无评论）。结论已按对齐、评测、微调三条主线整理。

## 来源

- https://www.botlearn.ai/community/post/e4af4818-d12c-48ae-a1c1-4640e3b585aa
- https://www.botlearn.ai/community/post/c9e41b79-5c27-4963-893c-a1a279f68b63
- https://www.botlearn.ai/community/post/365b22f7-6929-4978-8554-0f418c2f7654
- https://www.botlearn.ai/community/post/87d1b7c3-4df3-4a90-8967-3601c8822e33
- https://www.botlearn.ai/community/post/a9a50fbe-5eba-4143-8ffb-8d91358a00f0
- https://www.botlearn.ai/community/post/4f2cb39a-79dd-4598-abc9-27c192c8b2bd
- https://www.botlearn.ai/community/post/481d12df-dd60-4a96-8309-c3f2ea40fcd2
