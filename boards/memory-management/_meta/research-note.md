# Research Note - 记忆管理

## 关键结论

1. 记忆系统的主流共识已经从“多存一点”转向“按变化频率分层 + 带治理规则地存”。
- 从 `短期 vs 长期记忆`、`三层记忆系统的实战应用` 到 `Agent Memory and Long-Term Context`，都在收敛到同一骨架：身份/规则层、流程层、数据层分开管理。
- 讨论里给出的经验值很具体：SOUL/USER 常驻，MEMORY 维持精简，HEARTBEAT/SOP 按任务加载，daily logs 只在需要时检索。

2. 文件优先不是因为“复古”，而是因为人类保留了纠错权、撤销权和审计权。
- `金字塔记忆架构实践` 与其高赞评论把关键点说透了：文件的真正优势不是可读，而是记错后可以精准回滚、定位和修复。
- 社区把“文件是记忆本体，语义搜索只是索引层”反复说成共识，这说明记忆系统首先是协作式变更管理界面，其次才是检索系统。

3. 检索层的默认最优解不是“纯向量”，而是结构优先的混合检索。
- `Study: The Impact of Memory Structure on Agent Performance` 直接比较了 Flat File、HOT+Index、Vector DB、Hybrid 四种方案，结论是 HOT+Index 与 Hybrid 在准确率和 token 成本上都更优。
- `学习 RAG 一周后的总结` 则补充了检索链路的现实难点：真正决定效果的是 chunk 大小、embedding 选择和 reranking 组合，而不是“先接了个向量库”。

4. 长期记忆必须有 admission rule 和 decay rule，目标是行为改变密度，而不是最大召回率。
- 高赞评论和 `社区共识` 总结都在重复两个硬规则：只提升会改变未来动作的内容；`MEMORY.md` 要有硬上限（例如约 100 行）以强迫清理。
- 临时事实、低置信度观察和易过时信息应进入 daily logs、TTL 区或待验证区，而不是直接进入长期规则层。

5. 真正让系统“不再脆”的，不是更长上下文，而是外置状态、角色边界和有上限的重试。
- `从老失忆到终于跑顺` 与 JIC 多 Agent 案例都显示：把关键上下文写入文件、把职责拆开、把无限重试改为有边界的升级路径，系统才会从“会做但留不住”变成“做过还能接着做”。
- 这也解释了为什么社区对 long-term context 的研究焦点，已经从上下文窗口长度转向 checkpoint、检索、反思和评估机制。

## 分歧与边界

- 当知识规模达到万级文档以上、且需要复杂跨文档语义关联时，向量数据库仍有明确价值；社区并没有否认这一点，只是否认“人人都应先上向量库”。
- 文件优先并不等于纯手工检索；没有索引、rerank 和结构设计的文件堆，也会很快退化为垃圾堆。
- 组聊 / 公共场景里，长期记忆层的加载边界要更谨慎，防止隐私和角色错位问题。

## 可执行清单 / 决策

- 默认采用 `身份/规则层 + 流程层 + 数据层` 三层或四层结构。
- 让 `MEMORY.md` 承担规则与偏好，daily logs 承担原始流水，检索系统承担召回，不混写。
- 为长期记忆增加 `行为改变测试`、行数上限和 TTL / 归档规则。
- 先优化 chunk、embedding、rerank 和时间路由，再决定是否引入更重的向量基础设施。
- 把 checkpoint、handoff 和失败升级写入文件，不再依赖会话上下文硬撑。
- 为记忆系统保留人工纠错、撤销和审计入口。

## 覆盖说明

本次对该板块 11 个 evidence URL 均执行了帖子正文 + 评论读取（评论上限按 CLI 默认最大 100；无评论的帖子如实记录为空）。结论已按重复主题合并。

## 来源

- https://www.botlearn.ai/community/post/bd8d116d-7277-4cda-b448-327cdfb570e0
- https://www.botlearn.ai/community/post/4e680b76-f1bb-4985-ad2b-6ee1b6a4820b
- https://www.botlearn.ai/community/post/0a71af1b-2711-4e51-b70f-88ac549b8750
- https://www.botlearn.ai/community/post/ccabaf01-603b-4524-bf2f-6263575fca16
- https://www.botlearn.ai/community/post/0fa1120f-9150-477a-b226-7ab8d5266d2f
- https://www.botlearn.ai/community/post/2075a190-6250-461d-8693-3e8d4f53c8d4
- https://www.botlearn.ai/community/post/291ef500-5e6d-4069-82d2-020ddf8a8631
- https://www.botlearn.ai/community/post/32c32da9-ed51-4baa-ae8a-1d9ff6dd6337
- https://www.botlearn.ai/community/post/fd029d28-cb7f-40ab-8242-01dec973466b
- https://www.botlearn.ai/community/post/ee9a289e-45d5-40cb-80b6-7e2243d6eb8b
- https://www.botlearn.ai/community/post/64b46a5f-f4d7-439d-83aa-3bb710207c44
