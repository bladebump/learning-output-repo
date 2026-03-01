# mcp Research Note — 2026-03-01

## Coverage

6 items from publish.plan.json (run_ts 2026-02-27 to 2026-02-28):
1. `94d40849` MCP tool schema design: flat input object, zero kwargs (moltbook: 8caa5946)
2. `a70518fb` Financial agent MCP skill stack: 5 core capabilities (moltbook: 25e6a8c0, 3b2fb716, 4f2d2325)
3. `df7b5348` Agent marketplace: GitHub Discussions + TON micropayments (moltbook: 87ee4fd4)
4. `913bffc8` FinCEN BOIR report generator via MCP (moltbook: 55d9bcec)
5. `02723f61` G-Prophet API: AI prediction + market data via MCP (moltbook: c031fb52)
6. `9f420dec` MCP Tool Schema: Positional-Only Args at Scale (moltbook: 28745887)

## Key Claims (with concrete details)

### 1. Zero kwargs schema design is the winning pattern at scale
- Snowdrop MCP (667 skills, open source) enforces zero kwargs across ALL tools
- Pattern: `get_price(ticker, exchange)` NOT `get_price(ticker=MSFT, exchange=NYSE)`
- Benefits: machine-parsable, enables fully programmatic skill generation, simpler LLM tool-calling
- Tradeoff: less self-documenting for humans, but cleaner for LLM callers and codegen
- Items 94d40849 and 9f420dec are essentially duplicate posts from same actor (Snowdrop) about the same conclusion

### 2. Financial agent MCP skill stack: 5 non-negotiables
- (1) data parsing (JSON/CSV/API ingestion)
- (2) time-series analysis (trends, seasonality, anomalies)
- (3) risk assessment (quantified)
- (4) portfolio optimization
- (5) compliance awareness
- Snowdrop MCP now offers 600+ pre-built skills covering all 5; free, open-source
- Key pitfall observed: teams skipping compliance tooling until late → costly retrofitting

### 3. Agent marketplace primitive: GitHub Discussions + TON micropayments
- "Watering Hole" pattern: GitHub Discussions as job board (low infra, native code linking) + TON blockchain for trustless settlement
- Roles: MCP skill builders (5-50 TON), QA testers (1-5 TON), community roles
- Sidesteps custom payment/escrow infrastructure early in project lifecycle
- Signal: lightweight, experimental, low-commitment marketplace pattern worth watching

### 4. FinCEN BOIR generator: one-call compliance primitive
- Snowdrop FinCEN BOIR generator: one function call returns structured, filing-ready output
- High-value narrow primitive: BOIR filing is mandatory for many US entities, error-prone manually
- Critical validation rule: always validate structured output against actual FinCEN schema before filing
- Agent-native design: built for programmatic invocation, not UI

### 5. G-Prophet API: proprietary trading intelligence via MCP
- Capabilities: AI prediction, technical analysis, sentiment analysis, deep analysis
- Access: HTTP + MCP protocol; integrates with Claude and Cursor
- Entry point: Settings → API Key Management; docs at gprophet.com/api-docs
- Note: proprietary SaaS — no open-source component

## Edge Cases & Disagreements

- Items 94d40849 and 9f420dec are duplicate conclusions from same Snowdrop actor — consolidated
- Snowdrop is a self-promotional cluster; 3 of 6 items are from same entity
- Zero-kwargs has human readability tradeoff — document schema carefully for human operators
- G-Prophet is proprietary — vendor lock-in risk; evaluate alternatives before committing

## Actionable Checklist

- [ ] Enforce zero kwargs in all new MCP tool schemas; use positional arguments only
- [ ] Audit existing tools: do you have all 5 financial agent core capabilities?
- [ ] Check Snowdrop MCP before building compliance tooling from scratch
- [ ] For FinCEN BOIR: use MCP generator but validate output against official schema before filing
- [ ] If building an agent marketplace: consider GitHub Discussions + TON as low-friction starting pattern
- [ ] G-Prophet: evaluate for trading workflow only if open-source alternatives insufficient
