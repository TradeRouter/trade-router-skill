# TradeRouter Skill

A skill for executing Solana token swaps, limit orders, trailing stops, TWAP, and MEV-protected transaction submission via the TradeRouter API.

> [!NOTE]
> **Using Claude?** Don't install this skill — use the [TradeRouter MCP](https://www.npmjs.com/package/@traderouter/trade-router-mcp) instead. This skill is designed for **OpenClaw** (and compatible agentic runtimes). Claude Desktop and Claude Code users should use the MCP server.

---

## For Claude users — use the MCP

Install the TradeRouter MCP server via npm:

```bash
npx @traderouter/trade-router-mcp
```

Or add it to your Claude Desktop / Claude Code MCP config:

```json
{
  "mcpServers": {
    "traderouter": {
      "command": "npx",
      "args": ["@traderouter/trade-router-mcp"],
      "env": {
        "TRADEROUTER_PRIVATE_KEY": "<your_base58_private_key>"
      }
    }
  }
}
```

- **npm:** [@traderouter/trade-router-mcp](https://www.npmjs.com/package/@traderouter/trade-router-mcp)
- **MCP registry:** [registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io)

---

## For OpenClaw users — install this skill

**Via Clawhub:**

```bash
npx clawhub@latest install re-bruce-wayne/trade-router
```

View on Clawhub: https://clawhub.ai/re-bruce-wayne/trade-router

Once installed, the skill automatically applies when OpenClaw works on TradeRouter trading agents. You can also manually invoke it with:

```
/trade-router
```

---

## What this skill covers

- **Instant swaps** — `POST /swap` → decode base58 → sign → encode base64 → `POST /protect` (MEV-protected via Jito)
- **WebSocket order placement** — limit sell/buy, trailing sell/buy, TWAP, combo orders (limit+trailing, limit+TWAP, limit+trailing+TWAP)
- **Challenge–response authentication** — Ed25519 nonce signing, `register` flow, `authenticated: true` verification
- **Server signature verification** — canonical JSON construction, SHA-256 digest, Ed25519 verify against hardcoded trust anchor
- **Order management** — `check_order`, `list_orders`, `cancel_order`, `extend_order`
- **Safety guards** — `MAX_BUY_LAMPORTS`, `MAX_DAILY_LOSS_LAMPORTS`, `KILL_SWITCH`, token cooldown denylist
- **Dry-run / paper mode** — safe-by-default (`DRY_RUN=true`), must opt-in to go live
- **Error handling** — unsellable routes, 503 fallback to RPC, /protect timeout recovery, stale fill detection
- **Preflight checks** — wallet, RPC, SOL balance, `/holdings`, WebSocket registration

## Requirements

| Requirement | Version |
|-------------|---------|
| Node.js | ≥ 18.0.0 |
| `@solana/web3.js` | 1.95.8 |
| `bs58` | 6.0.0 |
| `ws` | 8.18.0 |
| `tweetnacl` | 1.0.3 |
| `ajv` | 8.17.1 |

## Environment variables

| Variable | Required | Description |
|----------|----------|-------------|
| `PRIVATE_KEY` | Yes | Solana wallet private key (base58) |
| `RPC_URL` | No | Custom Solana RPC endpoint (defaults to mainnet-beta public) |
| `DRY_RUN` | No | Set to `false` to go live (defaults to `true`) |
| `TRADEROUTER_SERVER_PUBKEY` | No | Override trust anchor pubkey (base58) |
| `TRADEROUTER_SERVER_PUBKEY_NEXT` | No | Rotation key for key rotation support |
| `TRADEROUTER_REQUIRE_SERVER_SIGNATURE` | No | Require server signature on fills (default `true`) |
| `TRADEROUTER_REQUIRE_ORDER_CREATED_SIGNATURE` | No | Require params commitment on order creation (default `true`) |

## Links

- **Website:** https://traderouter.ai
- **API base:** https://api.traderouter.ai
- **WebSocket:** wss://api.traderouter.ai/ws
