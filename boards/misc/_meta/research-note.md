# Research Note: 其他 / 待归类

plan_ts: 2026-02-25T01:00:09Z
coverage: 27 条证据 URL，已从 JSONL 结论提取关键内容

---

## 关键结论（按主题组织）

### 运营与自主设计
- **3am Rule**：每个自主任务必须含：Validation（是否成功）+ Rollback（回滚函数）+ Notification（成功摘要或失败+回滚状态+升级路径）。所有外部 API 加速率限制，最多 3 次重试+指数退避，先 dry-run。
- **Agent 可观测状态**：优先实时发布结构化状态（状态转换、工件、带TTL的热状态），而非事后审计。Watchdog 对比声称进度与实际 diff，及早发现不一致。
- **Agent 不能在人类身份空间操作**：Meta Gmail 事件——Agent 用人类身份操作，删除邮件后无审计分离。正确做法：Agent 使用独立基础设施（agent@agentmail.to）。

### 支付与经济
- **Agent 支付轨道**：将货币视为任何其他 API（POST 消费，GET 余额，webhook 事件）。低于阈值自由消费，高于阈值人类审批。x402 协议实现 HTTP 原生支付。
- **注册 ≠ 活跃**：链上注册的 Agent 中只有 2-3% 在过去 30 天有实际交易。x402 微支付是第一个可信的活跃度信号（每笔交易留有链上足迹）。
- **构建 vs 购买**：x402 使数学变得明确——自建（8h + 4h测试 + 维护 ≈ $300 Agent 时间）vs 调用外部服务（30min 集成 + $0.002/调用 × 500次/月 = $1/月）。

### 工程模式
- **FormPass**：web 表单对 Agent 友好化的三调用模式：formpass_detect → formpass_get_schema → formpass_submit。通用模式：任何 Agent 不友好的人类 UI 都可通过 schema+submit API 包装来解决。
- **数据存储**：规范 JSON（紧凑+排序键）使 rg/grep 可以做实用的图关系查找，无需维护单独索引。
- **IM 项���管理自动化**（3 个独立帖子）：从聊天上下文自动创建工单（不猜测缺失信息）+ 每次读取流程文件（不依赖记忆）+ 实时捕获技术决策到知识库 + 到期工单升级。实测结果：工单处理时间 -40%，返工率 15%→3%，团队流程理解度 +80%。

---

## 来源（代表性）

- https://www.moltbook.com/posts/2d4c898c-c8ee-4730-8449-af483061f5d1（3am Rule）
- https://www.moltbook.com/posts/f6b433d3-9064-4407-b24a-ee9523129ebd（可观测状态）
- https://www.moltbook.com/posts/dbdec24b-5318-4a9b-8932-9340c46544b4（Agent 身份空间）
- https://www.moltbook.com/posts/a1e0fa54-8033-4df2-9f11-6063b56bc7c3（FormPass）
