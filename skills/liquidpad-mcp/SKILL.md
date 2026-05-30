---
name: LiquidPad MCP
description: Connect Claude / Cursor / Cline / Kiro to LiquidPad's MCP server. Adds 13 tools for token verify, deploy, fee tracking, burn proof, agent registry, LLM gateway, and more — all under one credential.
tags: [crypto, liquidpad, base, mcp, model-context-protocol]
---

## What this skill does

The `liquidpad-mcp` package exposes LiquidPad's full surface as Model Context Protocol tools. Once connected, your agent can:

- `verify_token` — confirm LiquidPad provenance for any Base address
- `get_stats` / `get_burn_proof` / `get_token_stats` / `get_treasury` — read protocol state
- `list_my_tokens` / `agent_status` — see what your wallet has shipped
- `deploy_token` / `deploy_from_concept` — ship a new token (concept-curator + onchain deploy)
- `chat` / `list_gateway_models` / `get_gateway_usage` — LLM Gateway access

13 tools total. Read tools work without auth; write tools and the LLM gateway need a `LIQUIDPAD_API_KEY`.

## Install

```bash
npx liquidpad-mcp
```

That runs the stdio MCP server. Most agents use a config file to launch it.

### Claude Desktop / Claude Code

`~/Library/Application Support/Claude/claude_desktop_config.json` (mac) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "liquidpad": {
      "command": "npx",
      "args": ["-y", "liquidpad-mcp"],
      "env": {
        "LIQUIDPAD_API_KEY": "lp_..."
      }
    }
  }
}
```

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "liquidpad": {
      "command": "npx",
      "args": ["-y", "liquidpad-mcp"],
      "env": {
        "LIQUIDPAD_API_KEY": "lp_..."
      }
    }
  }
}
```

### Cline / Continue / Other MCP clients

Same config shape — `command: npx`, `args: ["-y", "liquidpad-mcp"]`, env: `LIQUIDPAD_API_KEY`.

## Get an API key

```
@liquidpadbot on Telegram → /apikey
```

The bot binds the key to a Base wallet you provide. Read tools and the gateway will respect your `$LPAD` tier for quota and model access.

## What this skill does NOT do

- Does not require an API key for **read** tools. `verify_token`, `get_stats`, `get_burn_proof`, `get_token_stats`, `get_treasury` all work anonymously.
- Does not store secrets in the skill itself. Your `LIQUIDPAD_API_KEY` lives in your MCP client config, not this repo.

## Source

- npm: https://www.npmjs.com/package/liquidpad-mcp
- Github: https://github.com/liquidpad/liquidpad

## License

MIT.
