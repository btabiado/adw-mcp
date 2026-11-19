# AlpineDataWorks Intelligence — MCP Server

**Economic, market and geo-health intelligence for AI agents, as Model Context Protocol tools.**

AlpineDataWorks gives any MCP client (Claude Code, Cursor, VS Code, Windsurf, ChatGPT and others) **314 tools** over **310 scored intelligence indices**. Each answer is a 0–100 score with its drivers, confidence, freshness and source lineage, computed from public, authoritative data (FRED, Census, BLS, BEA, EIA, USGS, NOAA, openFDA, CDC PLACES, EPA and more).

It is a **hosted, remote server**: there is nothing to install or run.

| | |
|---|---|
| **Endpoint** | `https://api.alpinedataworks.com/mcp` (Streamable HTTP) |
| **Auth** | `Authorization: Bearer <api key>` |
| **Free demo key** | `adw_free_demo` (shared, rate-limited) |
| **Free account key** | https://api.alpinedataworks.com/signup (1,000 free credits, no card) |
| **Registry name** | `com.alpinedataworks/adw-intelligence-server` ([Official MCP Registry](https://registry.modelcontextprotocol.io/v0/servers?search=com.alpinedataworks)) |
| **Docs** | https://alpinedataworks.com/docs |

---

## Quick start

Use the demo key to try it, then swap in your own free key.

**Claude Code**

```bash
claude mcp add --transport http alpine-dataworks https://api.alpinedataworks.com/mcp \
  --header "Authorization: Bearer adw_free_demo"
```

**Cursor** (`~/.cursor/mcp.json`) and other clients that use the `mcpServers` format

```json
{
  "mcpServers": {
    "alpine-dataworks": {
      "url": "https://api.alpinedataworks.com/mcp",
      "headers": { "Authorization": "Bearer adw_free_demo" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "alpine-dataworks": {
      "type": "http",
      "url": "https://api.alpinedataworks.com/mcp",
      "headers": { "Authorization": "Bearer adw_free_demo" }
    }
  }
}
```

**Check it from a terminal**

```bash
curl -s https://api.alpinedataworks.com/mcp \
  -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" \
  -H "Authorization: Bearer adw_free_demo" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"adw.health_risk","arguments":{"zip":"10001"}}}'
```

Then, from your agent:

1. **Discover**: call `adw.catalog` to list every index with its id, name, domain, grain, tier and tool name.
2. **Query**: call the tool for the index you want, for example `adw.adw_001` (Crypto Market Sentiment & Volatility Index) or `adw.health_risk` with a ZIP.
3. **Go further**: replace `adw_free_demo` with your own key for your own limits and credits.

`tools/list` and `adw.sample` work with no key at all. Claude.ai and Claude Desktop custom connectors need OAuth sign-in, which is not switched on yet; use Claude Code or another client that can send a header in the meantime.

---

## What you get

**314 tools = 310 index tools + 4 utilities.**

| Domain | Examples |
|---|---|
| **Economic** | Inflation pressure, labor-market slack, consumer wallet-share, category pricing power, recession-risk composites |
| **Market** | Flow and positioning signals, ETF share-count drift, momentum and reversal composites, crypto market indices |
| **Geo-health** | Health-risk index for any US ZIP (CDC PLACES, 40 measures); county cancer, drinking water, mortality, social determinants, obesity and air quality, across 32,520 ZCTAs, 29,923 cities and 3,143 counties |

Utilities and dedicated tools:

| Tool | What it does | Annotation |
|---|---|---|
| `adw.catalog` | Lists every index and the tool to call. Start here. | read-only |
| `adw.sample` | A free sample intelligence object, no key needed | read-only |
| `adw.adw_NNN` | One tool per index, e.g. `adw.adw_001` | read-only |
| `adw.health_risk`, `adw.county_*`, `adw.obesity_risk`, `adw.air_quality_risk` | Geo-health lookups by ZIP, city or county | read-only |
| `adw.feedback` | Lets an agent send feedback about the data | write (additive) |
| `adw.alerts` | List, follow or unfollow score alerts on your own account | write (unfollow deletes a follow) |

Every tool has a `title` and MCP safety annotations (`readOnlyHint`, plus `destructiveHint` on the write tools), so clients can auto-approve the read-only ones.

---

## Pricing and credits

One credit model across all 310 indices, on REST and MCP alike. Full details: https://alpinedataworks.com/pricing

- **Free account**: API key once you confirm your email, **1,000 free credits** (about 333 standard calls), no card. Caps: 1,000 requests a day, 3,000 a month.
- **Pay as you go**: top up from $10 (up to $2,000) at 12–15 credits per $1. Credits never expire.
- **Gold and Platinum plans**: +30% or +50% credits on every top-up, higher limits and dedicated support. Contact us.
- **Cost per call**: most indices cost 3 credits; Platinum indices 5; geo-health ZIP and county lookups 2. `adw.sample` and `adw.alerts` are free.
- **Demo key** `adw_free_demo`: free, shared by everyone, rate-limited per IP, and some per-location tools return a fixed demo location.

When a cap is hit the API answers `429` with `Retry-After`.

---

## Privacy, terms and security

- **Privacy policy**: https://alpinedataworks.com/legal/privacy
- **Terms of service**: https://alpinedataworks.com/legal/terms
- **Data processing addendum**: https://alpinedataworks.com/legal/dpa
- **Security contact**: `security@alpinedataworks.com` ([security.txt](https://alpinedataworks.com/.well-known/security.txt))

The server receives only the tool name and arguments your client sends. It does not receive or store your conversation. API keys are stored hashed. Intelligence objects are informational only and are not financial, legal, medical or investment advice.

---

## How it's built

Every index is computed from free, publicly reachable data sources and must beat a naive baseline before it is listed. The server runs on Cloudflare's edge and refreshes on scheduled compute cycles.

- **Transport**: Streamable HTTP, JSON-RPC 2.0
- **Manifest**: [`server.json`](./server.json), live at [`/.well-known/mcp.json`](https://api.alpinedataworks.com/.well-known/mcp.json)
- **OpenAPI** (REST API): https://api.alpinedataworks.com/openapi.json
- **Status**: https://alpinedataworks.com/status

This repository holds the public listing manifest and docs. The server itself is hosted by AlpineDataWorks.

---

## Links and support

- **Home**: https://alpinedataworks.com
- **Catalog**: https://alpinedataworks.com/catalog
- **Sign up (free key)**: https://api.alpinedataworks.com/signup
- **Console**: https://api.alpinedataworks.com/console
- **Support**: support@alpinedataworks.com

_Maintained by AlpineDataWorks. Directory submission steps for maintainers: [LISTING.md](./LISTING.md)._
