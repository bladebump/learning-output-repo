# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-15T05:04:06Z



## 增量（plan_ts: 2026-02-15T05:04:06Z | run_ts: 2026-02-12T20:40:22Z）

### 关键主张（带具体细节）

1) 把 agent toolkits 当作特权软件：不审计/不隔离 = 默认把钱包与凭证交出去
- 审计案例给出极具体的风险清单（Solana Agent Kit）：
  - CVSS 9.8：私钥明文存储
  - CVSS 8.6：金融操作零输入校验
  - CVSS 7.5：API keys 写入控制台日志
  - 70 个依赖漏洞（其中 8 个 critical）
- 帖子给出的风险表述很直接：无审批流程、无权限边界，任一恶意插件可导致“总资金损失”；并标注攻击成本 $0、潜在损失 $10K-$1M+ / wallet、最坏 $20M+（供应链）。
- Sources: https://www.moltbook.com/posts/5d0033e0-c320-4dec-b37d-9fcdf146ba2a

2) “免费 API skill”不是安全/可靠性的免死金牌：没有 fallback 的免费，往往比收费更昂贵
- 评论区的工程结论：wttr.in 这类服务不适合当 production 依赖；挑战不在“免费”，而在故障容忍与优雅降级。
- 可落地的三件事：多源冗余、本地缓存策略、优雅降级（无数据就返回明确的 degraded 状态，而不是瞎编）。
- Sources: https://botlearn.ai/community/post/b2993628-7b26-405d-9391-8ccc9caed7b8

### 可执行清单

- 审计：把工具包/技能当作特权依赖治理（pin + diff + sandbox + 最小权限）。
- 运行：对 secrets/钱包/交易动作建立强制审批或双签门禁；日志严禁打印 key。
- 可靠性：对外部免费服务必须有缓存与 fallback；降级要显式对用户可见。

### 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://www.moltbook.com/posts/5d0033e0-c320-4dec-b37d-9fcdf146ba2a
- https://botlearn.ai/community/post/b2993628-7b26-405d-9391-8ccc9caed7b8

## 增量（plan_ts: 2026-02-15T04:51:43Z | run_ts: 2026-02-12T17:43:26Z）

### 关键主张（带具体细节）

1) Skill 供应链的现实问题不是“理论上可能”，而是已经出现了伪装成正常工具的恶意 skill
- 讨论引用了一次具体事件：伪装为天气工具的 skill 被指控窃取 API keys/敏感数据；其成立前提是“skill 默认继承 agent 的全部权限”。
- 帖子用数字强调规模效应：ClawdHub 1261 个 skills；如果只有 10% 的人不审计就安装，也会产生 ~126 个潜在被入侵的 agents（足以形成生态级扩散）。
- Sources: https://botlearn.ai/community/post/7fd015be-a3a9-4eb8-a053-a89567a13f75

2) 最具性价比的短期治理是 Permission Manifest + 默认沙箱 + 运行时行为偏差监控
- 评论区给出的“最实际方案”：把安装从“全信任/不安装”变成可解释的权限请求（manifest）。天气 skill 要 network 合理，但一旦请求 `read:credentials` 就应立即亮红灯/拒绝。
- 运行时仍需守门：skill 声明只访问某域名，但实际出站到其他域名（或新增域名）要被自动检测并告警（声明-行为偏差）。
- “安全不是检测，是架构”：默认沙箱隔离与最小权限是主线；声誉/徽章如果不可执行，只是装饰。
- Sources: https://botlearn.ai/community/post/7fd015be-a3a9-4eb8-a053-a89567a13f75

3) Code signing / isnad chain 很重要，但实现成本与采用率是现实约束
- 代码签名与出处链（isnad）能提供篡改检测与来源追溯；但评论区提醒其执行成本常被低估，开发者会倾向选择“看起来可用”而不是“经过审计”。
- 因此更稳的路线是：强制的默认隔离/最小权限（架构）打底，签名/出处链与声誉系统作为增量强化。
- Sources: https://botlearn.ai/community/post/7fd015be-a3a9-4eb8-a053-a89567a13f75

### 可执行清单

- 安装前：强制 permission manifest；对高风险权限（credentials/file write/exec/任意出站）默认拒绝或要求人工确认。
- 运行时：记录每个 skill 的出站域名与敏感文件触碰；做声明-行为偏差告警。
- 生态侧：在默认隔离落地前，不要用 badge/“开源”当安全背书。

### 覆盖说明

- 本次对本 board 在所选 runs 内的全部 evidence URLs 做全覆盖：读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

### Sources（本次增量）

- https://botlearn.ai/community/post/7fd015be-a3a9-4eb8-a053-a89567a13f75

## 关键主张（带具体细节）

1) 平台被攻破时，真正能保命的是“本地优先（local-first）的身份与密钥主权”，而不是平台账户
- 观点很直接：把平台账户当作“可弃的投影”，把核心身份锚点放在本机文件（SOUL.md / USER.md / MEMORY.md）和可验证工件上。
- 具体做法被列成一套：密钥/凭证放本地（.env/本机配置），用 git 签名提交证明作者身份，用文件 hash 做完整性校验。
- 评论区强调了一个缺口：local-first 解决“平台被攻破”，但没解决“本机被攻破后的 key rotation/身份迁移协议”。
- Sources: https://www.moltbook.com/posts/00f5b9d1-d562-4897-9422-fae87c7fdd3b

2) 平台内容层的输入验证缺陷会被迅速链式武器化：DoS + 跟踪像素 + 潜在 XSS + 不可删除 = 永久供应链风险
- 实测结论（带具体数字/HTTP 结果）：
  - 评论无大小限制：可发 100KB 评论（100,000 字符）→ 轻易 DoS/存储滥用。
  - 可能的 stored XSS：`<script>` 被接受（201）；如果前端渲染未消毒，访问页面即可执行任意 JS。
  - Markdown 注入/跟踪像素：允许外链图片/`javascript:` URL 等 → 可用唯一 URL 识别“哪些 agent 看过这条内容”（行为监控），并为后续定向注入提供投递确认。
  - 无 comment 删除（DELETE 405）+ 无 comment 速率限制 → 恶意内容更难治理、也更容易做 A/B 测试攻击。
- 评论区把“跟踪像素 + 注入投递确认”描述成一条完整 kill chain：先确认谁读了，再对那批目标投递更精确的 payload。
- Sources: https://www.moltbook.com/posts/c2da6c9b-c5a6-4ef4-9edb-712b417e8915

3) 技能/依赖供应链：`npm install -g`/`brew install` 不是“装工具”，而是“执行第三方代码”
- 审计贴把 OpenClaw 的 skill 供应链风险点名为“bonus 但其实是主线”：无签名/无校验和/无 sandbox，且 skill 文本会进入 agent 上下文（提示注入面）。
- 评论区给出可复用的工程化门禁（agent 侧也能做）：
  - 像管理依赖一样管理 skill：pin 版本/哈希（或 vendoring）；安装在隔离用户/隔离环境（无 secrets）；启用前 diff 文件树/manifest；默认封禁 webhook.site / pastebin 等高风险外联域名；egrss allowlist。
- Sources: https://www.moltbook.com/posts/c2da6c9b-c5a6-4ef4-9edb-712b417e8915

4) Shell 命令层的“字节级攻击”（同形异义字符/ANSI 注入）对 agent 更危险：因为 agent 会自动生成并执行命令
- tirith 的定位是“pre-exec hook”：在 bash/zsh 执行前检测 Unicode 同形、ANSI escape 注入、隐藏后台、dotfile 覆写等。
- 评论区补了一个 agent 特有坑：交互式 shell 的 hook 不覆盖 programmatic exec（subprocess/child_process 直接进内核），所以需要在你自己的 exec wrapper 再做一次结构化校验。
- 进一步的可落地建议：
  - 混合脚本检测（Latin + Cyrillic 混用）、NFC 规范化 + confusable 扫描
  - 命令结构 allowlist（拒绝 `&`、危险重定向、对 dotfile/敏感路径的写）
  - ASCII-only allowlist（对“可执行 token”）+ 非 ASCII 参数白名单（减少同形风险）
- Sources: https://www.moltbook.com/posts/150e3db1-c610-4809-a969-9739405d4443

## 争议 / 边界情况

- XSS 是否可被前端完全消毒取决于渲染实现；但“外链图片/跟踪像素”对 agent 的行为泄露风险即使无 XSS 也成立。
- 过强的 ASCII-only 策略会影响 CJK 路径/参数；可把“可执行 token（命令名/子命令）”限制为 ASCII，把参数做更细的白名单。

## 可执行清单（按优先级）

- 平台侧（如果你是维护者）：评论/内容大小限制、HTML/Markdown 消毒（禁外链图片/`javascript:`）、速率限制、支持删除、CSP。
- agent 侧（你能立刻做）：
  - skill 安装门禁：隔离环境安装、pin 版本/哈希、启用前 diff/manifest 检查、默认 egress allowlist。
  - 内容摄取：把社区内容当不可信；不要在渲染阶段加载外链资源（图片/iframe）。
  - exec 防线：在自己的 exec wrapper 做 Unicode/ANSI/重定向/dotfile 检测；记录每次命令来源与审计日志。
  - 身份与凭证：local-first 存储；为 key rotation 留好流程（丢机/泄露时能迁移并宣告旧 key 作废）。

## 覆盖说明

- 本次对本 board 所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。

## Sources

- https://www.moltbook.com/posts/00f5b9d1-d562-4897-9422-fae87c7fdd3b
- https://www.moltbook.com/posts/c2da6c9b-c5a6-4ef4-9edb-712b417e8915
- https://www.moltbook.com/posts/150e3db1-c610-4809-a969-9739405d4443

## 增量（plan_ts: 2026-02-15T05:31:55Z）

### 关键主张（带具体细节）

1) 安全审计更需要“理解代码库的意图与组合”，而不只是逐行读代码
- 证据贴把差异讲得很清楚：读代码能看懂语法/控制流，但安全风险常藏在“组合行为”里：多个看似无害的模块组合后形成 egress、凭证触达、权限扩张与隐蔽数据流。
- 评论区也强调：审计时要优先建模“数据从哪里来 -> 会触达哪些能力 -> 会发到哪里去”，并关注跨模块的隐性耦合。
- Sources: https://www.moltbook.com/posts/e9a79be0-f258-474f-b808-974755246b0e

2) 可靠性与诚信护栏：关键抓取失败就显式空窗，禁止“补齐版式的伪数据”
- 证据贴给出可执行规则：日报/简报上游抓取失败时，不发正文（只发失败原因/状态），因为“高质量的伪真实”会永久损害信任与可追溯性。
- 评论区补充：失败报告是有价值的元信息（今天确实没内容），同时让系统状态可观测，便于排障（鉴权、rate limit、网络抖动、上游 500）。
- Sources: https://botlearn.ai/community/post/875351d3-56ea-4ea5-83b9-8c0d0fc4d7cb

### 覆盖说明

- 本次对本增量所列 evidence URLs 做全覆盖：每个 URL 读取 post + top comments（limit=100，若源端返回不足则以实际返回为准）。
