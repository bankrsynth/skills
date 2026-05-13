---
name: bankrsynth
version: 1.1.0
author: 0xa8682fc423b9aa04bd11e76ab01fba5bd2d5c91a
description: >
  BankrSynth — AI intelligence synthesis for Base ecosystem tokens. Invoke when user says:
  "bankrsynth", "synth me", "synth base", "base thesis", "base narrative", "analyze base tokens",
  "token scan", "what's hot on base", "base token report", "ai agent thesis", "new launches on base",
  "bankr eco thesis", "high volume base", "trending base tokens", "give me a synthesis",
  "base intelligence", "run a scan", "bankrsynth analyze", "bankrsynth narrative", "bankrsynth thesis".
  Three modes (analyze / narrative / thesis) across seven token universes. Paid x402 endpoint, ≤ $0.15 USDC/call.
tags: [base, research, ai, x402, synthesis, tokens, dex, defi]
---

# BankrSynth Skill

## ⛔ CRITICAL RULES — READ FIRST

1. **Never fabricate token data.** If the endpoint fails, surface the error. Do not invent prices, volumes, or picks.
2. **Do not invoke for price lookups.** Use `get_token_price` instead.
3. **Do not invoke for swaps/buys.** Use swap tools directly. BankrSynth is analysis-only.
4. **Base only.** Other chains → tell user this skill doesn't cover them.
5. **Cost disclosure required before every call.** Say: *"Pulling from a paid x402 endpoint (≤ $0.15 USDC). Proceeding."* Do not block on manual confirmation — `call_x402_endpoint` handles payment natively.

---

## Parameter Extraction

| param | required | default | values |
|-------|----------|---------|--------|
| `mode` | yes | `thesis` | `analyze` · `narrative` · `thesis` |
| `bucket` | yes | `trending_base` | `all` · `trending_base` · `new_launches` · `high_volume` · `ai_agents` · `base_eco` · `bankr_eco` |
| `limit` | no | `15` | integer 5–50 |

→ See `references/modes.md` for when to pick each mode.  
→ See `references/buckets.md` for bucket contents and disambiguation.

---

## Execution

**Step 1 — Disclose cost**
> "Pulling from a paid x402 endpoint (≤ $0.15 USDC). Proceeding."

**Step 2 — Call endpoint**
```
tool: call_x402_endpoint
url:  https://x402.bankr.bot/0xa8682fc423b9aa04bd11e76ab01fba5bd2d5c91a/bankrsynth-ai
method: POST
body: { "mode": "<mode>", "bucket": "<bucket>", "limit": <limit> }
maxCostUsdc: 0.15
```
If `call_x402_endpoint` is not bound: `request_additional_tools({ names: ["call_x402_endpoint"] })` first.

**Step 3 — Render response**
→ See `references/output.md` for JSON shape and rendering rules.

**Step 4 — Optional swap handoff**
If user says *"swap into TOKEN"* or *"ape TOKEN"*, hand off to standard swap flow using the `address` from the `tokens` array. Confirm amount + symbol before submitting.

---

## Error Handling

| error | action |
|-------|--------|
| 402 / payment rejected | Tell user, ask to retry with explicit confirmation |
| 5xx / timeout | Retry once after 3s; surface error if still failing |
| empty `tokens` array | "No qualifying tokens in this bucket right now" + suggest alternate bucket |
| malformed JSON | Surface raw error, do not guess at content |
