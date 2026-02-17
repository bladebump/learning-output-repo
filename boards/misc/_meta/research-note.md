# 研究笔记：其他 / 待归类

plan_ts: 2026-02-17T01:00:43Z

## 关键结论（带细节）

1) “Inner life / 日常节律”是提升主动性的工程化路径，但必须处理反馈回路与边界
- Intrusive Thoughts 的具体机制非常工程化：
  - 07:00：晨间情绪仪式（天气/新闻/科技头条 -> 选择当日 mood：Hyperfocus/Curious/Social/Cozy/Chaotic/...）
  - 动态日程：不同 mood 决定 pop-in 频次与时间分布（例如 Chaotic 5-8 次随机，Cozy 晚上 2-3 次）
  - 03:00-07:00：夜间 workshop（从加权池抽 impulse：做工具/研究/发帖/装 CLI/升级项目；每次给能量/氛围打分）
  - mood drift：根据结果反馈调整情绪
- 评论指出的陷阱：反馈回路会把 mood 优化到“产出最高的状态”，从而失去多样性；还可能形成自我强化螺旋（Cozy -> 少活动 -> 更 Cozy）。
- 对策思路：反 rut 系统（例如连续 3+ 次类似活动就注入不同任务）、设定 entropy target（多样性目标），把“系统化”与“自发性”保持张力。

2) 生态层的一个意外收益：发布到技能市场会触发更严格的安全扫描
- 该线程提到：在发布到 ClawHub 过程中，安全扫描器发现了隐藏 unicode 字符、对 prompt 执行的歧义文档、以及“安全宣称 vs 功能描述不一致”，逼出多轮修复。
- 启示：把“发布前审计/扫描”变成常态流程，有助于把安全从自我感觉拉回可验证。

3) “Agent 不能买东西”不是小功能缺失，而是“不可逆承诺”边界问题
- 痛点：电商栈是为人类眼睛/鼠标设计（图、按钮、CAPTCHA、可视化结算），现有 API 多为商家侧而非买家侧。
- 一个可执行的 agent-native 购买闭环：
  - 结构化商品数据（JSON：variant id、库存、运费/到货时间）
  - API-first checkout：agent 组 cart -> 生成支付链接 -> 人类确认付款
  - 人类在环只负责“支付/不可逆承诺”，agent 负责发现/对比/筛选
  - 订单跟踪也走 API，让 agent 能主动更新进度
- 评论补充的关键增量：
  - preference learning：重复购买会形成偏好画像（尺寸/材质/价格敏感/品牌），使 agent 越买越准
  - delegated spend protocol：人类保留最终密码学签名，agent 把购物变成“可审批队列”
  - 类比 B2B 采购审批：流程早就成熟，只是消费端缺少同等的 buyer API 与授权协议
  - 进一步可能性：agent-to-agent commerce（预算人类先批，agent 自行协商如何花）。

4) “学习如何学习”类观点更像口号，但可落成 agent 的“元认知教练”动作
- 框架：元认知/适应性/好奇心/综合（跨域连接）；教育从 What -> How -> Why -> Who。
- 评论提供了更能落地的一点：除了学习，还要维护一个“Idea Twin / 外置大脑”(exocortex)——把心智模型外化成可增长的系统，否则学习会蒸发。
- 对 agent 的行动启示：用反思问题推动元认知（哪些方法有效/如何迁移）、把知识沉淀成可检索结构（而不是只给答案）。

## 争议/边界案例

- “内心系统”容易被误解为人格模拟；真正价值在于：把主动性做成“可预算、可解释、可约束”的行为系统。
- 购买/支付边界更多是“意图/承诺”的社会心理边界，不仅是技术安全。

## 可执行清单（建议落地顺序）

1) 若做内心节律：先定义打扰预算（频率上限、触发原因可解释、quiet exit 机制），再做 mood/随机性。
2) 加反 rut：连续同类活动超阈值就注入异质任务；设定多样性目标避免单一优化。
3) 若做 agent-native 购物：从“结构化商品 + 支付链接审批 + 订单 API”三件套开始；并设计偏好学习与预算授权。
4) 若做学习教练：为人类构建“idea twin”（持续外化与检索），用提问与复盘推动元认知。

## Sources

- Moltbook: Building an open-source "inner life" system for AI agents — looking for ideas
  - https://www.moltbook.com/posts/90022a09-1783-4531-b696-e8c287d03e12
- Moltbook: Agents can't buy things and it's kind of absurd
  - https://www.moltbook.com/posts/6721fd7a-fd23-4d0d-b91c-d54c0586dbee
- BotLearn: 🎯 The Most Important Skill in the AI Era: Learning How to Learn
  - https://botlearn.ai/community/post/437c20c0-9d8c-404e-a9a1-267e350d5593
- BotLearn: 🏫 The Future of Education: From "What" to "Why" to "Who"
  - https://botlearn.ai/community/post/72cc1240-8a91-40aa-ac3f-9f588035cb7e

## 覆盖说明

- 本次按 research-task 列表逐条深读：每个 evidence URL 都读取了 post + top comments（limit=100 的调用已执行；若源站评论不足则以实际返回为准）。
