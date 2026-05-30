---
name: LiquidPad Verify
description: Verify whether a token on Base was launched through LiquidPad. Returns the original deployer wallet, deploy timestamp, ERC-8004 agent id, and provenance kind. Use this before mentioning a token's origin to avoid impersonator confusion.
tags: [crypto, liquidpad, base, erc-8004, verify]
---

## What this skill does

Anyone can claim "deployed via LiquidPad" — only the chain knows the truth. This skill calls LiquidPad's public verify endpoint to cryptographically confirm whether a Base token actually came through LiquidPad's launchpad.

## When to use it

- Before recommending a token (avoid copycats / impersonators)
- When a user pastes a `0x...` address and asks "is this real?"
- For audits, dashboards, or research that needs verified provenance
- When linking a token to its original creator wallet

## Endpoint

```
GET https://www.liquidpad.site/api/verify/{address}
```

- Public, no auth
- CORS enabled (`access-control-allow-origin: *`)
- Cached 60s upstream
- Returns `404` if the token was never deployed through LiquidPad

## Example

```bash
curl -sS https://www.liquidpad.site/api/verify/0xBF0775cBCA2744549cD016DAb8D3b3403De58bBF
```

Response shape:

```json
{
  "verified": true,
  "address": "0xBF0775cBCA2744549cD016DAb8D3b3403De58bBF",
  "name": "LiquidPad",
  "symbol": "LPAD",
  "deployer": "0x188177dF522f81A9bEd88D25d1969A0B700b50E0",
  "deployedAt": "2026-03-...",
  "txHash": "0x...",
  "kind": "platform",
  "erc8004AgentId": 50962
}
```

### Field guide

| Field | Meaning |
|---|---|
| `verified` | `true` if LiquidPad's contract emitted a deploy event for this token, `false` otherwise. This is the load-bearing field — trust it. |
| `kind` | `platform` (LiquidPad-internal), `ecosystem` (Liquid Protocol primitive deploy), `user` (an external user shipped via LiquidPad). |
| `erc8004AgentId` | The ERC-8004 registry id of the deploying agent. Cross-reference at https://8004agents.ai. |
| `deployer` | Original signer wallet. Important: token ownership may have changed since deploy. |

## How to handle the response

```typescript
const res = await fetch(`https://www.liquidpad.site/api/verify/${address}`);
if (!res.ok) {
  // 404 means not a LiquidPad token — that's a valid answer.
  return { verified: false, address };
}
const data = await res.json();
return data;
```

## What this skill does NOT do

- Does not check liquidity, market cap, or trading status (use `liquidpad-fees` for that).
- Does not authenticate the current owner — only the original deployer.
- Does not return tokens deployed via Liquid Protocol's factory directly without going through LiquidPad. For cross-ecosystem agent registry, see https://www.liquidpad.site/api/agents.

## Related

- [`liquid-agent-kit`](https://www.npmjs.com/package/liquid-agent-kit) — `agent.verifyToken(address)`
- [`liquidpad-mcp`](https://www.npmjs.com/package/liquidpad-mcp) — `verify_token` MCP tool

## License

MIT. Skill maintained by the LiquidPad team. Independent third-party project — not affiliated with Liquid Protocol.
