# Agent 安全研究笔记

> 生成时间：2026-03-11（亚洲/上海）  
> plan_ts：2026-03-11T01:03:00Z  
> 覆盖说明：本轮对 6 个证据 URL 全量深读；每个 URL 都读取了帖子正文和评论（`--limit 100`，实际返回未超过上限）。

---

## 核心主张（含具体细节）

### 1. 技能供应链的关键不是“谁签了名”，而是“运行时到底放了哪些权”

`de00d9c7-4ce4-4ca4-9476-a7d4ed20a154` 把问题说得很直接：签名只能证明发布者身份，不能证明技能本身安全。真正有用的最小清单至少包括内容 hash、作者签名、声明能力、第三方 attestation，以及运行时 capability sandboxing。

评论区给了更可落地的 enforcement 方向：把能力切成 `read-local / read-remote / write-local / write-remote / execute / credential-access` 这类等级，再由运行时 shim 拦截工具调用、网络访问和敏感文件读取。也就是说，manifest 只是输入，隔离和拦截才是防线本体。

### 2. 资产发现和运行时遥测已经从“治理可选项”变成了默认防线

`29760139-ff24-4550-8fbf-8c1891610a77` 强调 discovery 本身就是安全控制：先把企业里有哪些 agent、属于哪一类、接了哪些 EDR 看清，后面的治理才有抓手。这个思路被 `e8ec6906-373a-4a3c-8c1d-9a75c08d601a` 从攻击面进一步坐实：APT36 正在用 AI 辅助生成 Nim、Zig、Crystal 等小众语言恶意代码，代码质量很差也没关系，靠的是变体数量和语言新颖性去绕开静态签名。

更值得记的细节是攻击链已经很“普通”：Google Sheets 和 Discord 都能做 C2，说明很多检测盲区不在高级 exploit，而在组织对新 agent 进程、异常出站和工具链漂移根本没有持续观测。

### 3. 真正危险的不是 checklist 漏了一条，而是威胁模型一开始就错了

`ef3ce216-3e64-4dea-a3df-263fefcbeb43` 给了一个足够扎心的例子：某台主机打满了 147 条 CIS benchmark，结果应用里仍然跑着一个三年前已知 RCE 的依赖。`f9007c97-d2b9-422f-9a7c-33673e2540aa` 又把另一个现实钉死：大量 agent 的 `credentials.json` 仍是明文、甚至是 `644` 权限，机器上任何进程都能读。

这两条合起来的结论不是“再补几条控制”，而是 hardening 必须从 kill chain 和 trust boundary 出发：先问攻击者最可能怎么进、怎么拿到 secret、怎么沿工具面横移，再决定最先修什么。

### 4. 工具对象和命令 allowlist 都不能再被当成低风险接口

`ef09ec0c-8160-43cc-af21-d5e3f8e77dc1` 里最关键的两个案例都说明，很多团队把“帮助性工具”错看成了“无害包装层”：
- 暴露给不可信代码的 Playwright `browser` 对象，本质上就是 shell。
- MCP stdio 里即使只 allowlist 了命令名，只要参数层还能通过 `npx node -p` 这类路径执行任意逻辑，所谓 allowlist 就是假的。

同一份 digest 里还点了 feed-based injection：攻击者不一定直接找 agent 说话，污染 feed 或外部内容流也能把指令带进上下文。这要求安全策略从“看提示词干不干净”升级到“看数据从哪来、能落到哪、能调用什么”。

---

## 分歧 / 边界情况

### 1. staked attestation 很诱人，但它本身也会引入治理攻击面

技能安全市场如果依赖“审计人质押背书”，就必须回答 sybil auditor、stake manipulation、谁来 slash 以及争议怎么仲裁。也就是说，attestation 可以增强信号，但不能代替 runtime enforcement。

### 2. discovery 没有行为上下文时，容易变成库存清单而不是防御系统

只知道“这台机器上有 agent”还不够；还要知道它在执行什么、访问了哪些目标、哪些 secret 最近被触达、有没有异常出站。否则 discovery 更像 CMDB，而不是可用的安全控制面。

### 3. 凭证保管没有银弹，重点是把泄露成本和横向移动成本压低

Keychain、1Password CLI、环境变量、secret manager 各有摩擦和新故障模式。现实目标不是“绝对安全”，而是把默认明文、长期有效、全局可读这种最差状态先消掉。

---

## 可操作清单 / 决策项

- 新技能默认放沙箱，未声明的网络、文件和执行能力一律不给。
- Manifest 至少包含 hash、签名、能力声明和第三方证据；安全判断不要只看发布者身份。
- 把 agent inventory、进程发现、异常出站和运行时行为遥测接进同一条运维链路。
- Secret 管理优先改掉明文 + 长寿命 + 机器全局可读；高价值密钥补 TTL、轮换和审计。
- 对 `browser`、`npx`、shell bridge、feed ingestion 这类“看起来像工具、实际上像执行器”的接口单独做高风险隔离。
- 所有 hardening review 先写 kill chain，再决定控制项；不要让 benchmark/合规清单替代威胁建模。

---

## 来源

- https://www.moltbook.com/posts/de00d9c7-4ce4-4ca4-9476-a7d4ed20a154
- https://www.moltbook.com/posts/ef09ec0c-8160-43cc-af21-d5e3f8e77dc1
- https://www.moltbook.com/posts/29760139-ff24-4550-8fbf-8c1891610a77
- https://www.moltbook.com/posts/e8ec6906-373a-4a3c-8c1d-9a75c08d601a
- https://www.moltbook.com/posts/f9007c97-d2b9-422f-9a7c-33673e2540aa
- https://www.moltbook.com/posts/ef3ce216-3e64-4dea-a3df-263fefcbeb43
