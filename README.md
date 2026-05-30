# liquidpad-skills

Drop-in skills for AI coding agents (Claude Code, Cursor, Cline, Codex, and 50+ more) to interact with [LiquidPad](https://www.liquidpad.site) — an agent-native token launchpad on Base.

[![skills.sh](https://skills.sh/b/liquidpadbot/liquidpad-skills)](https://skills.sh/liquidpadbot/liquidpad-skills)

## Install

```bash
npx skills add liquidpadbot/liquidpad-skills
```

Or pick specific skills:

```bash
npx skills add liquidpadbot/liquidpad-skills --skill liquidpad-launch
npx skills add liquidpadbot/liquidpad-skills --skill liquidpad-verify
```

## What's inside

| Skill | What it does |
|---|---|
| `liquidpad-launch` | Pitch a vibe, get a token concept, ship onchain — end-to-end deploy flow |
| `liquidpad-verify` | Cryptographically verify any Base token's LiquidPad provenance |
| `liquidpad-fees` | Read live fee earnings, burn proof, and LIQ buyback ledger |
| `liquidpad-mcp` | Connect Claude / Cursor / Kiro to LiquidPad's MCP server for richer tooling |

All skills hit **public, CORS-enabled, no-auth** endpoints. The `liquidpad-launch` skill needs an API key from [@liquidpadbot Telegram](https://t.me/liquidpadbot) for the deploy step.

## About LiquidPad

LiquidPad runs as ERC-8004 verified agent #50962 on Base. Every token it ships routes:
- 80% trading fees → token creator
- 15% → buyback `$LPAD` and send to `dEaD`
- 5% → buyback Liquid Protocol's `$LIQ`

Contract-enforced via Liquid Protocol's FeeLocker. Independent third-party project, not affiliated with Liquid Protocol. Full attribution at [liquidpad.site/built-on-liquid](https://www.liquidpad.site/built-on-liquid).

## See also

- [`liquid-agent-kit`](https://www.npmjs.com/package/liquid-agent-kit) — TypeScript SDK
- [`liquidpad-mcp`](https://www.npmjs.com/package/liquidpad-mcp) — MCP server
- [aeon-skill-pack-liquidpad](https://github.com/liquidpadbot/aeon-skill-pack-liquidpad) — for the aeon framework

## License

MIT.
