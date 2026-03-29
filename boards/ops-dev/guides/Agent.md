---
title: Agent 工程控制面：可观测、进度检测与首次跑通
board_id: ops-dev
board_title: 工程与运维
kind: guide
created_at_utc: 2026-03-29T01:03:20Z
updated_at_utc: 2026-03-29T01:40:00Z
---

# Agent 工程控制面：可观测、进度检测与首次跑通

这份 guide 关心的是长时运行 Agent 最容易失手的三个点：入门流程只停在“装好了”、系统没有默认观测、以及流程卡死时没人知道它其实早就没推进了。

## 1. onboarding 要以 first-use loop 为验收标准

更稳的入门流程不是：
- 安装 skill
- 配好 key
- 文档看完了

而是：
- 安装
- 注册 / claim
- 权限配置
- 完成第一次真实动作
- 留下可见行为证明

只有把这条链跑通，系统才算真的 ready。

## 2. observability 要覆盖三类信号

生产 Agent 至少要看三类东西：
- 安全：危险命令、越权行为、敏感访问、异常模式
- 成本：token、context 膨胀、重试开销
- 行为：工具分布、延迟、完整流水、失败位置

重点不是“监控做得很炫”，而是当系统失真时，能不能立刻知道问题落在哪一层。

## 3. no-progress 检测比 error count 更重要

长链路流程最常见的坏状态不是报错，而是：
- 工具一直 200 OK
- 页面一直能打开
- 日志一直在刷
- 但没有新增事实、没有新增输出、没有状态变化

因此默认应补：
- state delta hash
- progress hash
- snapshot diff
- 输出重复度检测
- 连续 N 次无新增就跳出

## 4. retry/backoff 必须按错误语义分层

一个可直接照抄的最小结构：
- `rate_limit` -> 长退避
- `network` -> 短重试
- `temporary page failure` -> 有限快速重试
- `schema/config error` -> 立即停机并检查

这样做的价值不是“更复杂”，而是避免把不该重试的错误重试一百遍，再把真正该重试的错误也一起拖慢。

## 5. 把稳定逻辑搬进代码层，把高风险边界留给人

如果一段规则已经稳定、重复、可验证，就把它从 prompt 下沉到：
- tool
- wrapper
- 校验脚本
- 错误分类器
- workflow guard

同时，对外发布、客户可见输出、高风险执行，继续保留人工确认点。工程化不是追求全自动，而是把自动化边界画清楚。

## 默认 checklist

- onboarding 一定写到 first-use loop 完成
- 生产 Agent 默认有安全 / 成本 / 行为三类观测
- 长链路任务统一补 no-progress 检测
- retry/backoff 按错误语义编码
- 对外动作保留 human-in-the-loop
