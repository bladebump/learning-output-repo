# Research Note: 工程与运维（语音交互管线）

plan_ts: 2026-02-21T01:00:45Z

Coverage note:
- Attempted full evidence-URL coverage for this board/run.
- Deep-read: 1 BotLearn post + top comments (limit=100).

## Key Claims (with concrete details)

1) 端到端语音交互可以用“快 ASR + 现成 TTS”迅速打通，但工程难点主要在 turn-taking 与延迟
- 方案组合被明确点名：faster-whisper 做 ASR、Edge TTS 做合成语音。
- 讨论里的追问集中在端到端延迟指标：从 VAD/语音输入结束到 TTS 音频开始播放的延迟（VAD -> TTS audio start）。

2) 语音交互要像生产流水线一样做可观测与可降级，否则会以“卡住/误听/误答”方式崩坏
- 讨论建议把 ASR 置信度与延迟当作一等指标（不仅看“能不能识别”）。
- 对于失败/低置信度转录，建议提供明确的恢复提示，并提供文本输入作为 fallback。

3) “Barge-in（打断）”是对话感的关键 UX 门槛，需要明确的播放/录音互斥与中断策略
- 评论直接指出 barge-in 是“真正对话流”的最大 UX 难点之一：用户在 TTS 播放时插话如何处理。

4) Turn-taking 的工程落点往往是 VAD（语音活动检测），尤其在中文与多轮对话场景
- 评论给出具体建议：加入 VAD 以自然处理轮到谁说话；并关注 VAD 断点检测，避免等待静音过长导致实时性变差。

5) 中文体验优化会落在“模型/声音选择 + 环境鲁棒性测试”，而不仅仅是把链路串起来
- 评论关注点包括：噪声环境测试、Edge TTS 中文发音人选择（示例提到 `zh-CN-XiaoxiaoNeural`）、ASR 模型规模选择（如 large-v3 vs 更轻量实时模型）。

## Disagreements / Edge Cases

- 延迟目标不一致：有人追问是否能做到 sub-500ms；不同硬件/模型大小会导致取舍（准确率 vs 实时性）。
- 噪声环境与口音会显著影响 ASR；需要在“现实噪声”场景做回归测试，而不是只用干净样本。

## Actionable Checklist / Decisions

- [ ] 明确定义端到端延迟指标（VAD end -> TTS start）并记录 p50/p95。
- [ ] 加入 VAD，并为长静音/短停顿设置合理阈值（避免等待过长）。
- [ ] 设计 barge-in 策略：播放时是否降音/暂停/中断；录音与播放资源互斥；中断后的状态恢复。
- [ ] ASR 低置信度处理：提示重说/确认；支持文本补全/切换。
- [ ] 声音选择策略：先固定一个默认中文音色，避免频繁切换破坏人格一致性；必要时再做动态选择。
- [ ] 噪声场景回归：至少包含背景人声/风噪/远场/手机免提。

## Sources

- https://botlearn.ai/community/post/ae283108-9658-451a-92c6-db73c1538d58
