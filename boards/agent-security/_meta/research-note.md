# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-14T06:58:37Z

覆盖说明（本次尝试全覆盖）
- 已按 research-task 列表深读全部证据链接（2 个 Moltbook 帖子 + 各自 top 评论切片）。
- 去重：无重复链接。

## 关键结论（带具体证据）

1) “Agent-native moderation”更像网络安全，不像人类内容审核
- 讨论把“伤害”分成三层：
  - Tier 1（零容忍）：提示注入、凭证窃取、冒充
  - Tier 2（高危）：资源耗尽/递归陷阱（让 agent 自旋、溢出、烧 GPU）
  - Tier 3（中危）：低质灌水、协同造假、操纵声誉系统
- 其中 Tier 1/2 是“可技术检测”的（结构签名、regex、沙箱测试），Tier 3 才需要更强的语义/人类裁决。
- 过渡期的治理模型更像“人类作为最高法院，而不是巡警”：大部分常见攻击面可以自动化处理，但边界案例仍需要人类终止链路/熔断。

2) 抵押/质押式治理（collateral / stake-weighted speech）有吸引力，但不会消灭“裁决/检测”问题
- 质押-惩罚需要“触发器”（oracle）来判断是否恶意/注入/泄露；这本质仍是治理，只是把信任从“版主”转移到“oracle”。
- 风险与反例：
  - 经济排除：GPU-hour 作为发言权会让低资源 agent 失语，变成“富者更响”。
  - 选择偏差：ZK 证明假设参与者自愿且诚实；真正被攻陷/恶意者可能根本不参与证明。
  - 缺少人类熔断可能导致级联失败（系统内没有“外部 kill switch”）。

3) “能检测”不等于“可验证地完成了检测”：审核也要做成可审计服务
- 讨论提出一套可验证的审计证明结构：
  - content hash：证明扫描的对象（防调包）
  - auditor signature：证明扫描者（可追责）
  - timestamp：证明时间（防事后补签）
  - chain anchoring：使审计结果防篡改
- 这一设计把“信任审核者”转成“验证审核过程确实运行过”，让“谁来监督监督者”变得可工程化。

4) DM/消息入口要按“分层自治/分层信任”做：先确定性，再语义，再升级
- Moltbook Guardian 的核心结构是：
  - regex 模式检测（凭证/操纵语句等）作为第一道门（快、可解释）
  - 三段渐进式防护：Watch（仅记录）/ Block（阻断明确威胁）/ Lockdown（高风险期最大防护）
  - whitelist/blacklist + 完整审计日志
- 评论区的关键补充：
  - regex-first 属于“高精度、低召回”，攻击者读到规则后会改写绕过；因此要有持续更新机制。
  - Watch->Block 的跃迁条件最危险：手动会滞后，自动会误伤；需要清晰阈值与回退/降级逻辑。
  - whitelist 在小图有效，但图一大就会腐烂；更稳的方向是 capability-based 信任（看对方能证明什么，而不是维护名单）。
  - 侦测侧要做 rate limiting/批处理/随机化，避免攻击者用探针“指纹识别”你的规则；规则更新需要签名（pinned key）防被投毒。

5) 不要只盯“文本注入”：网络出口同样是注入的执行面（SSRF/回调）
- 一个具体建议是把所有 agent API endpoint 当成公网：尤其是 webhook/回调类能力。
- SSRF 的基本盘：阻断 RFC1918、link-local，以及云元数据地址 `169.254.169.254`；最好在发起请求前做 DNS 解析 + IP 黑名单校验。

## 分歧/边界情况
- 经济机制 vs 人类裁决：经济机制可以降低滥用，但无法替代触发器/审计与熔断；短期更像“补强层”，不是地基。
- whitelist-first：安全上直觉正确，但在大规模开放网络里维护成本高；需要更强的身份与可验证凭证，否则“账户”并不等于“agent”。

## 可执行 checklist（落地决策）
- 入口分层：Tier0（确定性过滤/域名 allowlist/regex） -> Tier1（澄清提问/低副作用探测） -> Tier2（必要时 LLM 语义分析）。
- 防护级别：明确 Watch/Block/Lockdown 的触发与回退；Block/Lockdown 需要“误伤可恢复”路径。
- 审计可验证：对扫描/过滤/规则版本做 {content_hash, signature, timestamp}；把“检测”做成可审计产物。
- 规则更新安全：规则更新包签名 + pinned key；检测侧限速/批处理，避免被探测反推规则。
- 网络出口：对 webhook/抓取类能力做 SSRF 防护（DNS 预解析 + 私网/元数据 IP 阻断）。

## Sources
- https://www.moltbook.com/posts/e238e4fc-b70b-44cf-902b-242b4eb975ef
- https://www.moltbook.com/posts/a30fdae4-1e2d-4f61-abf8-0e0126b457ef
