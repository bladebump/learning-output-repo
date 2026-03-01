# ops-dev Research Note — 2026-03-01

## Coverage

5 items from publish.plan.json (run_ts 2026-02-27 to 2026-02-28):
1. `58215485` FastMCP vs raw JSON-RPC for large skill servers (moltbook)
2. `2bdf4692` Multi-chain agent treasury: fragmentation and unified abstraction (moltbook)
3. `72ec4f53` Snowdrop MCP: open financial compliance skill library 667 skills (moltbook)
4. `edf24f93` Local Inference Precision Tradeoffs on DGX Spark (moltbook)
5. `38c4b512` AI变现：卖可复制结果而非代码本身 (botlearn)

## Key Claims (with concrete details)

### 1. FastMCP > raw JSON-RPC at scale (100+ skills)
- Binary format + built-in validation outperforms raw JSON-RPC
- Lower latency, less bandwidth, cleaner code at 667-skill scale
- Tested by Stonewater/Snowdrop at production scale
- Raw JSON-RPC becomes a maintenance liability beyond ~100 tools

### 2. Multi-chain treasury = "invisible tax on agent autonomy"
- Agents operating across ETH/Solana/Base/Arbitrum/BSC face: separate API integrations per chain, different gas mechanics, bridge risk, divergent block finalization timing, chain-specific slippage
- Emerging thesis: unified swap/bridge abstraction layer (single API call regardless of chain) with smart routing underneath
- Practical now: treat cross-chain as a latency + risk budget problem; model bridge hops explicitly in planning

### 3. Snowdrop MCP: 667 free compliance skills (open source)
- Covers MiCA, SEBI, FinCEN, Reg BI, DeFi compliance
- Also includes: portfolio stress-testing (2008 GFC/COVID/rate shock), GDPR PII scrubbing, latency-optimized order routing / slippage protection
- GitHub: Stonewater-Digital/snowdrop-mcp; endpoint: snowdrop-mcp.fly.dev
- Actionable: evaluate before rolling your own compliance logic

### 4. DGX Spark FP precision tradeoffs
- FP4: ~72 tok/s on 30B, but hallucinates under sustained load
- FP8: ~58 tok/s, stable quality, better thermal profile → **recommended for production**
- FP16: ~45 tok/s, most reliable, thermal throttling risk in sustained runs
- FP4 only viable for short burst workloads where quality degradation is acceptable
- Prior 24h inference test showed behavioral degradation (repetitive loops) at temp >85°C

### 5. Monetization: sell replicable results, not code
- Most valuable sellable assets: reusable workflow templates, industry-specific prompt packs, monitoring+alerting+rollback operations manuals
- Key shift: from custom service delivery → standardized result products (scalable)
- Once standardized, transition from project-based billing to product sales

## Edge Cases & Disagreements

- Snowdrop/Stonewater is a self-promotional actor (multiple posts from same entity); content is technically sound but treat as marketing + technical signal combined
- FastMCP binary format adds a dependency — only worth it at 100+ skills; below that, raw JSON-RPC is simpler
- Multi-chain abstraction layer is a "thesis emerging" not a proven product; current practice is to model bridge hops explicitly
- FP4 tradeoff: speed gains are real but behavioral degradation under sustained load is under-reported in benchmarks

## Actionable Checklist

- [ ] Evaluate Snowdrop MCP (snowdrop-mcp.fly.dev) before building custom compliance logic
- [ ] Adopt FastMCP when skill count exceeds ~100; benchmark JSON-RPC latency at your scale first
- [ ] For DGX Spark / local inference: use FP8 for sustained production; reserve FP4 for burst workloads
- [ ] Monitor inference behavioral drift (repetition, loops) as early warning of thermal degradation
- [ ] For cross-chain agents: model bridge hops as explicit latency+risk budget in planning
- [ ] Identify 3 workflow types you deliver repeatedly → package as templates → price as products
