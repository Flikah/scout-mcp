# Scout MCP

> Real estate agent directory + tools for AI assistants. A product of Flika Realty LLC.

Scout publishes a public [Model Context Protocol](https://modelcontextprotocol.io) server that any Claude, Cursor, Copilot, or custom AI agent can call to:

- Look up Scout-registered real estate agents by geography
- Search active MLS listings in SC and GA
- Submit a buyer or seller lead and route it to a licensed agent
- Subscribe to proprietary seller-intent signals (probate, NOD, FSBO)

Scout is operated by **Flika Realty LLC**, a dual-state real estate brokerage licensed in South Carolina and Georgia (SC License #135886).

## Live endpoint

```
https://scout.realestate/api/mcp
```

- Transport: **HTTP / JSON-RPC 2.0**
- MCP protocol version: `2025-03-26`
- Status: v0.1 (public beta)

## Tools

| Tool | Auth | Description |
|---|---|---|
| `scout.coverage` | anon | Where Scout covers natively (SC, GA) + states with signed referral partners |
| `scout.search_listings` | anon | MLS listings by city, ZIP, price, beds |
| `scout.find_agent` | anon | Scout-pin agents live in a territory (60-min freshness window, license-verified only) |
| `scout.route_lead` | API key | Submit a buyer/seller lead; routes to Flika agent or partner brokerage |
| `scout.get_seller_signals` | API key | Probate / NOD / distressed-property feed (SC + GA, admin-approved) |

## Install in Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or the equivalent path on Windows/Linux:

```json
{
  "mcpServers": {
    "scout": {
      "url": "https://scout.realestate/api/mcp",
      "transport": "http",
      "headers": {
        "Authorization": "Bearer flika_live_YOUR_KEY_HERE"
      }
    }
  }
}
```

Anonymous read tools (`coverage`, `search_listings`, `find_agent`) work without the Authorization header. Write tools (`route_lead`, `get_seller_signals`) require a Scout API key.

## Request an API key

Email [spencer@flikah.com](mailto:spencer@flikah.com) with your brokerage or use-case and we'll issue a key. Comped for individual agents; standard terms for brokerages (see [Referral terms](#referral-terms) below).

## Quick test — no install required

```bash
curl https://scout.realestate/api/mcp \
  -H 'content-type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call",
       "params":{"name":"scout.coverage","arguments":{}}}'
```

## Referral terms

For `scout.route_lead`:

- **Buyer-side:** 30% of the receiving agent's commission
- **Listing-side:** 25% of the receiving agent's commission
- **Tail:** 18 months from first introduction
- **Payment:** net-30 after funded close

## Agent verification

Every Scout-listed agent is license-verified before their pin becomes discoverable via `scout.find_agent`. Verification checks the state real estate commission's public licensee record (SC: LLR; GA: REC) and uses Claude-powered fuzzy name matching. A pin only surfaces in search results if the agent's license is currently `active`.

## Rate limits

- Anonymous: 30 requests / minute / IP
- Keyed: 300 requests / minute / key
- `route_lead` writes: 20 / day / key (adjustable for signed partners)

## Source + ops

Scout's backend + consumer surface is in a private Next.js monorepo. This repo hosts the public MCP spec and client-facing docs only. Contact Spencer Wilkinson for partnership / integration inquiries.

- Consumer site: [scout.realestate](https://scout.realestate)
- Brokerage (operator): [flika.realestate](https://flika.realestate)
- Contact: [spencer@flikah.com](mailto:spencer@flikah.com)

## License

MIT — the metadata + docs in this repo are MIT. The hosted server itself is proprietary (operated by Flika Realty LLC).
