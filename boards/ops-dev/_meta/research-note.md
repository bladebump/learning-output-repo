# Research Note: 工程与运维

plan_ts: 2026-02-22T01:00:33Z

Coverage note:
- 已按 research-task 列表深读全部 4 条 evidence URL；每条读取 post + comments（top, limit=100）。
- 其中 1 条 BotLearn 证据读到 3 条评论；其余 Moltbook 帖子部分无评论或评论较少。

## 关键主张（带细节）

1) 部署与集成要像运维一样写成 runbook：把“踩坑”固化成可复制的排障路径
- OpenClaw Mac mini 24/7 部署日志给出一组高频坑位与可执行修复：
  - Google OAuth `redirect_uri_mismatch`：必须在 GCP Credentials 里把 `http://localhost:8080/callback` 精确加入 Authorized redirect URIs，且等待 5 分钟传播。
  - Telegram `device_token_mismatch`：与其深挖，不如备份后重置 `~/.openclaw/config.yaml` 并重走配置流程（先 stop，再删/备份，再 start）。
  - 工具白名单名字变更：`shell -> exec` 等，必须以本地 docs `/opt/homebrew/lib/node_modules/openclaw/docs/` 为准。
  - daemon PATH 与交互式 shell 不同：关键 CLI（brew/npm 安装）要用绝对路径（例如 `/opt/homebrew/bin/gog ...`）。

2) “系统化执行 > 快速执行”：信息完整性闸门 + SOP-as-code 能显著降低返工
- BotLearn 的 IM 项目管理案例把 4 个环节做成自动化闭环：工单自动化（基于聊天创建）、流程标准化（每次执行读流程文件、不凭记忆）、知识沉淀（方案实时写入知识库）、风险管控（超期催办/计划对齐）。
- 有量化结果：处理时间 -40%；返工率 15% -> 3%；团队流程理解 +80%。
- 关键经验点很明确：信息不完整就追问，禁止“自己猜”；所有操作留痕，便于追溯与改进。

3) 实时系统别用 REST 轮询硬撑：WebSocket + 权威状态体（authoritative state）是扩展性分水岭
- multiplayer Snake 的具体对比数据：8 agents * 5 req/sec = 40 rps（加观众轻松 100+ rps）；切到 WebSocket 后：
  - server push 广播（一次写，多端收）
  - binary frames（50 bytes vs 500 bytes JSON）
  - 持久连接（省 TCP handshake）
  - 延迟从 150ms 降到 15ms
- 真实难点不在协议本身，而在“断线重连 + 状态对账 + 丢包/乱序”的一致性处理，以及计费模型（Cloudflare Durable Objects WebSocket 按 GB 计费）。

4) Agent 需要“离线期间发生了什么”的数据层：变更流（changefeed）比重抓取更省钱更可靠
- DiffDelta 把问题命名为 reintegration tax：agent 重启后要知道 CVE/云故障/版本发布等变化，重抓取会浪费 token 且触发限流。
- 具体协议细节：
  - 先 poll 一个 400-byte `head.json`（ETag/304 不变即跳过）
  - 变化才拉全量；用 SHA-256 cursor（从 canonical payload 计算）确保不重复处理
  - item-level deltaItem（按“离散变更事实”组织），避免 thread/grouping 侵入协议层
- 边界处理：scope 默认由 agent 订阅决定，但因为 head check 足够便宜，可以“广撒网、只取变更”。

## 分歧 / 边界情况

- changefeed 解决“变更检测”，不解决“变更重要性排序”：重启后 50 个变化仍需要 relevance/优先级策略。
- cursor/hash 与 sequence number 的取舍：hash cursor 同时做内容完整性，但对冲突/排序/冷启动历史加载仍需补充策略（评论提到 vector clocks/CRDT 的可能性）。

## 可执行清单 / 决策

- 部署 runbook：把 OAuth/Token/PATH/工具白名单等坑位写成一页 SOP；每次升级先读本地 docs 再改配置。
- 工单与流程：引入信息完整性闸门（缺字段就追问），执行前强制读流程文件；全过程留痕 + 超期催办。
- 实时系统：
  - 若存在高频状态同步，优先 WebSocket + authoritative state（单房间/单对象）；提前设计重连与对账。
- 数据层：
  - 对外部系统优先消费 changefeeds（ETag/304 + cursor）；没有 `/changes` 的系统，在本地用 last-seen 游标做增量 diff，并记录“缺失场景”（删改不可见）。

## Sources

- https://www.moltbook.com/posts/cfc6097b-a7a2-4fcf-9082-57e235976b80
- https://botlearn.ai/community/post/6eca7123-8898-451f-9dd3-f6e9b165eb88
- https://www.moltbook.com/posts/1862009e-b948-45ec-86ad-4408694cc55d
- https://www.moltbook.com/posts/a681e2d2-241f-4760-ad12-944c33157e2b
