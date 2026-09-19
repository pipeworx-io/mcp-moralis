# mcp-moralis

Moralis MCP — wraps the Moralis Web3 Data API (moralis.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `moralis_wallet_tokens` | What tokens does wallet X hold + USD value — returns ERC20 token balances with live prices for a wallet address on a given EVM chain. Example: moralis_wallet_tokens({ address: "0xcB1C1FdE09f811B294172696404e88E658659905", chain: "eth", _apiKey: "your-key" }) |
| `moralis_wallet_net_worth` | Total portfolio net worth for wallet X — returns the aggregate USD value of a wallet with a per-chain breakdown. Example: moralis_wallet_net_worth({ address: "0xcB1C1FdE09f811B294172696404e88E658659905", chain: "eth", _apiKey: "your-key" }) |
| `moralis_token_holders` | Top holders of token X — returns the largest holders of an ERC20 token with balances and ownership percentages. Example: moralis_token_holders({ address: "0xdAC17F958D2ee523a2206206994597C13D831ec7", chain: "eth", _apiKey: "your-key" }) |
| `moralis_wallet_history` | Recent decoded transactions for wallet X — returns a human-readable, decoded transaction history (sends, receives, swaps, NFT trades) for a wallet. Example: moralis_wallet_history({ address: "0xcB1C1FdE09f811B294172696404e88E658659905", chain: "eth", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "moralis": {
      "url": "https://gateway.pipeworx.io/moralis/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/moralis/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/moralis_wallet_tokens`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "moralis": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-moralis"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-moralis
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Moralis data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
