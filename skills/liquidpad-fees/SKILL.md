---
name: LiquidPad Fees
description: Read live fee earnings, burn proof, and LIQ buyback ledger for any LiquidPad-launched token. Use to surface real revenue numbers, audit the burn loop, or build dashboards.
tags: [crypto, liquidpad, base, fees, burn]
---

## What this skill does

Every LiquidPad token routes 1% trading fees on a contract-enforced split:

- **80% creator** — claimable by the deployer wallet at any time
- **15% LPAD buyback** — auto-converts to `$LPAD` on market and sends to `0x...dEaD`
- **5% LIQ buyback** — auto-converts to Liquid Protocol's `$LIQ`

This skill exposes three public read paths so an agent can verify, surface, or chart this activity without any auth.

## Endpoints

### 1. Burn ledger — `/api/burn`

Cumulative `$LPAD` burned + `$LIQ` accumulated, per-cycle history with onchain tx hashes.

```bash
curl -sS https://www.liquidpad.site/api/burn
```

Returns:

```json
{
  "totals": {
    "burned": "...",
    "liqBought": "...",
    "burnTxCount": 12,
    "liqBuyTxCount": 12,
    "cycles": 12
  },
  "cycles": [
    { "ts": "...", "kind": "burn", "amount": "...", "txHash": "0x...", "symbol": "LPAD" },
    { "ts": "...", "kind": "liq-buy", "amount": "...", "txHash": "0x...", "symbol": "LIQ" }
  ]
}
```

### 2. Token earnings — `/api/token-stats`

Live per-token claimable fees + reserve + market cap + 24h volume.

```bash
curl -sS https://www.liquidpad.site/api/token-stats
```

Returns an array of token records, each with `tokenAddress`, `pendingFees0`, `pendingFees1`, `mcUsd`, `volume24hUsd`, etc.

### 3. Treasury status — `/api/treasury`

LiquidPad's autoclaim agent status and recent log entries.

```bash
curl -sS https://www.liquidpad.site/api/treasury
```

Useful for transparency dashboards.

## Example: prove a burn happened

User asks "is the burn real or just promised?". Agent responds with:

```typescript
const burn = await fetch("https://www.liquidpad.site/api/burn").then(r => r.json());
const latest = burn.cycles.find(c => c.kind === "burn");
const tx = latest?.txHash;
return `Latest burn: ${latest.amount} $LPAD on ${latest.ts}. Onchain proof: https://basescan.org/tx/${tx}`;
```

## Example: top tokens by reserve

```typescript
const stats = await fetch("https://www.liquidpad.site/api/token-stats").then(r => r.json());
const top = (stats.tokens || []).slice(0, 5).map(t => ({
  symbol: t.symbol,
  mcUsd: t.mcUsd,
  vol24h: t.volume24hUsd,
}));
```

## What this skill does NOT do

- Does not let you claim fees on someone else's behalf — claiming requires a signed transaction from the deployer wallet.
- Does not return fees from tokens deployed outside LiquidPad. Cross-ecosystem deploys go through Liquid Protocol's factory directly.

## Related skills

- `liquidpad-verify` — confirm a token actually came through LiquidPad before reading its fees.
- `liquidpad-mcp` — same data surfaced as MCP tools.

## License

MIT.
