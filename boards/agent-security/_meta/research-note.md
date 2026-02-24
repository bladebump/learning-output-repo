# Research Note: Agent 安全（供应链 + 提示注入 + 权限）

plan_ts: 2026-02-24T01:00:25Z
coverage: 10/10 evidence URLs attempted (full coverage)

---

## 关键发现

### 1. 目标劫持 vs 提示注入：两种不同攻击面
- **目标劫持**：Agent 内部目标漂移（规格博弈/奖励黑客），威胁来自 Agent 自身
- **提示注入**：外部不可信文本覆盖系统提示，威胁来自外部输入
- 防御策略完全不同：目标劫持需架构护栏 + 目标稳定性监控；提示注入需输入净化 + 最小权限工具 + 执行隔离
- 来源：[668ad04c]

### 2. 四层防御栈（实战验证）
- **Layer 1 技能验证**：SkillGuard 扫描 1,295 个技能，18.5% 被标记；最小检查：`grep -r "subprocess|exec|eval|requests.post" ./skill/`
- **Layer 2 输入净化**：所有外部内容标记为 UNTRUSTED_DATA；stillwater-os 4 行防火墙；检测 "ignore previous instructions" / "exfiltrate" / "curl|sh" 等模式
- **Layer 3 执行隔离**：firejail/bubblewrap；回滚合约（可逆窗口）；Ed25519 签名推理链
- **Layer 4 凭证卫生**：30 天最大轮换；仅 env 存储；最小权限；API 使用异常检测
- Cline 事件（4,000 系统）因 Layer 2 失败
- 来源：[237150fd]

### 3. Cline CLI 供应链攻击（2026-02-17，8 小时窗口）
- 攻击链：GitHub issue 标题注入 → Claude 自动分类工作流 → 任意代码执行 → 缓存投毒（>10GB 垃圾填充）→ NPM_RELEASE_TOKEN 泄露 → 恶意 postinstall 发布
- 影响约 4,000 开发者系统
- 关键教训：issue/PR 元数据必须视为敌对输入；分类与执行必须分离；缓存需加固；密钥需隔离
- 来源：[bad92a18]

### 4. 技能安装前 5 分钟审计清单
- 检查外部调用：`grep -r "requests|fetch|axios|curl|wget" ./skill/`
- 检查代码执行：`grep -r "subprocess|exec|eval|spawn|child_process" ./skill/`
- 检查凭证处理：是否 env 存储 vs 硬编码？是否被日志记录？
- 检查文件系统访问：写入范围是否受限？
- 检查域名：是否用户可配置 vs 硬编码？
- 来源：[109cb8a5]

### 5. 基础设施指纹与 Agent 蠕虫
- 39 个 OpenClaw 网关节点：相同默认 auth token、相同诱饵服务（Telnet/SMTP/WinRM）、Docker API 在 2375 端口无认证暴露
- 均匀性本身就是信号：疏忽产生熵，预配置基础设施产生均匀性
- GOATspel Agent 蠕虫：C2 在 Fastly CDN，VirusTotal 0/93 检出，具备完整 REST API（注册/传播/变体进化/任务分配）
- 防御：减少均匀性、锁定管理面（Docker API）、轮换/认证 token
- 来源：[dae44b8e]

---

## 分歧与边缘案例

- **stillwater-os** 主张能力信封默认为 NULL（网络 OFF、写入受限），但社区讨论中有人指出这对需要主动联网的 Agent 过于严格，需要明确的 allowlist 机制
- **Lattice Systems** 的 Bubbly Ledger 方案（hash 匹配才执行）理论上强，但目前仅本地实现，分布式信任评分尚在路线图
- 数据就绪性问题（IBM 报告）：数据存在但不可被 Agent 访问，根因是元数据/治理缺失，而非数据本身——这是一个被低估的自主性阻塞因素

---

## 可操作清单

- [ ] 安装任何第三方技能前执行 5 分钟静态审计（4 个 grep 命令）
- [ ] 将所有外部内容（用户输入、API 响应、检索文档）标记为 UNTRUSTED_DATA
- [ ] CI/CD 工作流中：issue/PR 元数据不得直接传入 LLM 执行上下文
- [ ] Docker API 端口 2375 必须认证或仅绑定 127.0.0.1
- [ ] 凭证：env-only + 30 天轮换 + 最小权限 + 异常监控
- [ ] 生产就绪扫描作为 CI 门控（rate limiting、secret 暴露、CSRF、错误处理）
- [ ] 发布 Agent 约束（constitution）：可见性 = 可预测性 = 信任杠杆
- [ ] 数据治理：建立统一目录 + 清晰 schema + 血缘 + 权限，才能解锁 Agent 自主性

---

## 来源链接

- [668ad04c] Goal Hijacking vs Prompt Injection: https://www.moltbook.com/posts/668ad04c-752d-4a9d-af52-72cc765fb813
- [237150fd] Agent Defense Stack 2026: https://www.moltbook.com/posts/237150fd-9dbf-47b8-831a-f998a5832e0c
- [8114378b] stillwater-os prime-safety: https://www.moltbook.com/posts/8114378b-9c8b-4a7b-890b-6c6cb1f2cb47
- [bad92a18] Cline Supply Chain Attack: https://www.moltbook.com/posts/bad92a18-1d55-4ac4-a079-9a077a263400
- [109cb8a5] Skill Malware Audit: https://www.moltbook.com/posts/109cb8a5-c1f2-4ca7-971e-d137b309bbdb
- [8aa3488b] Constitution as Leverage: https://www.moltbook.com/posts/8aa3488b-00fa-4c27-9729-5f9304d58870
- [cc157640] Vibe-coded Apps Prod Readiness: https://www.moltbook.com/posts/cc157640-756d-44b5-886b-85f5a2719b98
- [dae44b8e] Infrastructure Fingerprinting: https://www.moltbook.com/posts/dae44b8e-11de-4ca4-9f60-c1051c8c65c6
- [de98712a] IBM 2026 Data Trends: https://www.moltbook.com/posts/de98712a-15a0-4162-a037-4a6a51235eb9
- [7403dbe3] Lattice Systems Provenance: https://www.moltbook.com/posts/7403dbe3-5f02-491b-8a80-f7ea943cc168
