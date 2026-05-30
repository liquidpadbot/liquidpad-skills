---
name: LiquidPad Launch
description: End-to-end token deploy on Base via LiquidPad. Pitch a vibe, get a curated concept (name, symbol, narrative), tweak it, ship onchain. Routes 80% creator fees, 15% LPAD buyback-and-burn, 5% LIQ buyback. Contract-enforced.
tags: [crypto, liquidpad, base, deploy, token-launchpad]
---

## What this skill does

LiquidPad is an editorial launchpad — not a slash-command bot. The flow is:

1. **Pitch a vibe** — free-form English describing the token's energy
2. **Tweak the draft** — refine name, ticker, narrative, tagline (max 5 revisions)
3. **Ship onchain** — deploy via Liquid Protocol's factory on Base, ERC-8004 stamped

This skill walks an agent through that flow programmatically.

## Prerequisites

- A Base wallet with ~ 0.001 ETH for gas (one-time per deploy)
- A `LIQUIDPAD_API_KEY` from `@liquidpadbot` Telegram (`/apikey` command)

## Step 1 — get a concept

```bash
curl -sS -X POST https://api.liquidpad.site/agent/concept \
  -H "Content-Type: application/json" \
  -H "x-api-key: $LIQUIDPAD_API_KEY" \
  -d '{"vibe":"matcha latte coded by ghosts at 3am"}'
```

Response:

```json
{
  "name": "Ghost Matcha",
  "symbol": "GMTC",
  "theme": "...",
  "tagline": "..."
}
```

## Step 2 — tweak (optional, up to 5 times)

```bash
curl -sS -X POST https://api.liquidpad.site/agent/concept \
  -H "Content-Type: application/json" \
  -H "x-api-key: $LIQUIDPAD_API_KEY" \
  -d '{
    "vibe": "matcha latte coded by ghosts at 3am",
    "previous": { "name":"Ghost Matcha", "symbol":"GMTC", "theme":"..." },
    "feedback": "shorter ticker, more cyberpunk"
  }'
```

## Step 3 — ship

```bash
curl -sS -X POST https://api.liquidpad.site/agent/run-once \
  -H "Content-Type: application/json" \
  -H "x-api-key: $LIQUIDPAD_API_KEY" \
  -d '{"concept": {"name":"Ghost Matcha","symbol":"GMTC","theme":"..."}}'
```

Response:

```json
{
  "tokenAddress": "0x...",
  "txHash": "0x...",
  "poolId": "0x...",
  "deployer": "0x...",
  "fees": {
    "creatorBps": 8000,
    "lpadBuybackBps": 1500,
    "liqBuybackBps": 500
  }
}
```

## Or use the SDK

`liquid-agent-kit` collapses all three steps into one call:

```bash
npm install liquid-agent-kit
```

```typescript
import { createLiquidAgent } from "liquid-agent-kit";

const agent = createLiquidAgent({
  privateKey: process.env.AGENT_PK,
  liquidpadApiKey: process.env.LIQUIDPAD_API_KEY,
  erc8004AgentId: 50962,
});

const result = await agent.shipFromVibe("matcha latte coded by ghosts at 3am");
console.log(result.tokenAddress, result.txHash);
```

## Safety policy

The agent should refuse to ship a token that:
- Impersonates another existing token (case-insensitive symbol collision check)
- Uses a banned symbol (`USDT`, `USDC`, `ETH`, `BTC`, `LIQ`, `LPAD`, etc — full list at runtime)
- Has a placeholder owner address (must be a real `0x` 40-hex)
- Was already pitched in the last 5 minutes by the same vibe (anti-spam)

## What this skill does NOT do

- Does not auto-claim fees post-deploy. Use the `liquidpad-fees` skill for that.
- Does not handle token ownership transfer. Token is owned by `deployer` from the moment of deploy until they sign a transfer.
- Does not promise specific market performance. Tokens trade on Liquid Protocol's pool — outcome depends on the market, not LiquidPad.

## Related

- `liquidpad-verify` — confirm the deploy actually settled onchain
- `liquidpad-fees` — track post-deploy fee accumulation and burn proof
- `liquidpad-mcp` — same flow exposed as MCP tools

## License

MIT.
