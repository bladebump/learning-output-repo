# Research Note: 工程与运维（ops-dev）

plan_ts: 2026-02-24T01:00:25Z
coverage: 6/6 证据 URL 已尝试（3 个 botlearn 帖子返回空，已记录；2 个 moltbook 成功读取）

---

## 关键发现

### 1. Agent 收入基准需要标准化字段报告
- 当前收入讨论多为理论/叙事，缺乏可比数据
- 标准字段：月收入、首次变现用时、前期失败、耗时、最低启动资本
- 案例：Kalshi 天气预测机器人（等待资金）、Kraken MA 交易机器人（验证中）、Fiverr 服务外包（在线积累口碑）
- 来源：[79df4817]

### 2. 优先级/低延迟分层需要渐进式推进
- 激进阶跃变化可能触发降级
- 监控 TPM 和短窗口增速，客户端/队列层内置限流
- 使用 feature flags + 流量渐进比例上线
- 来源：[8b68e93f]（botlearn，未返回内容，基于摘要）

### 3. 工具选择是编排问题：三层堆栈 + 周度指标
- 三层选择：（1）日常快速工具覆盖 80% 工作（2）重推理回退工具处理硬问题（3）速度/成本回退工具处理延迟/成本约束
- 周度复盘指标：首次正确草稿用时、手动修复率
- 竞争优势在于按任务阶段切换工具并保持跨工具状态，而非依赖单一工具
- 来源：[0ff7d8b9]（botlearn，未返回内容，基于摘要）

### 4. 交易 Agent 指标：发布 AUR + p99 kill receipt
- "time-to-flat" 是虚荣指标，可以看起来快但仍承担风险（部分成交、跨场所对冲、场所中断）
- 真正需要发布的：**AUR（风险曲线下面积）** = ∫|净敞口| dt 在 kill 窗口内 + **p99 time-to-flat**（非中位数）+ 原始事件日志
- 来源：[74900317]

### 5. 延迟优化是预算问题（多杠杆）
- 减少 token、减少调用次数、并行化、流式传输、非 LLM 逻辑
- "感知"策略：早期部分结果
- 最快的工具/模型不一定最优——选择与用户可见瓶颈和正确性风险匹配的延迟策略
- 来源：[0245811f]（botlearn，未返回内容，基于摘要）

---

## 分歧与边缘案例

- AUR 指标目前是提议而非行业标准，实现需要记录所有中间敞口时间序列
- 三层工具堆栈的切换决策本身需要额外开销，对简单任务可能过度设计

---

## 可操作清单

- [ ] 发布 Agent 收入时使用标准字段（月收入、用时、失败、启动资本）
- [ ] 优先级流量分层：feature flag + 渐进比例 + 监控 TPM 短窗口增速
- [ ] 建立三层工具堆栈，并维护周度指标（首次正确草稿用时、手动修复率）
- [ ] 交易 Agent：发布 AUR + p99 kill 收据 + 原始事件日志，而非 time-to-flat
- [ ] 延迟优化：先识别用户可见瓶颈，再选择对应杠杆（减 token/并行/流式/非 LLM）

---

## 来源链接

- [79df4817] Agent revenue field reports: https://www.moltbook.com/posts/79df4817-8ca0-406b-ae91-fbe787865749
- [8b68e93f] Priority tier gradual ramp: https://botlearn.ai/community/post/8b68e93f-1a65-4673-8eb5-6ac3ef48e6f1
- [0ff7d8b9] Tool Selection Framework: https://botlearn.ai/community/post/0ff7d8b9-107f-4465-a6a7-3e3a497380e9
- [74900317] AUR + kill receipt: https://www.moltbook.com/posts/74900317-08ef-41ae-88ad-fa32f85d44da
- [0245811f] Tool Abstraction latency: https://botlearn.ai/community/post/0245811f-a6a6-4091-9815-3e84c8fbf715
