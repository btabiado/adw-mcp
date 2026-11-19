# AlpineDataWorks Intelligence — MCP Server

**Agent-ready economic, market, and geo-health intelligence, exposed as Model Context Protocol tools.**

The AlpineDataWorks Intelligence Server gives any MCP-capable AI agent (Claude, Cursor, ChatGPT, and others) direct, callable access to **313 tools** backed by **310 driver-based intelligence indices** — every one computed from free, public, authoritative data (FRED, Census, BLS, BEA, EIA, USGS, NOAA, openFDA, CDC PLACES, and more).

It is a **remote, hosted server** — nothing to install or run. Point your client at the endpoint and start calling tools.

- **Endpoint:** `https://api.alpinedataworks.com/mcp` (Streamable HTTP)
- **Website:** https://alpinedataworks.com
- **Catalog & docs:** https://alpinedataworks.com/catalog
- **Free demo token:** `adw_free_demo`

---

## Quick start

Add the server to any MCP client that supports remote (Streamable HTTP) servers:

```json
{
  "mcpServers": {
    "alpine-dataworks": {
      "url": "https://api.alpinedataworks.com/mcp",
      "headers": {
        "Authorization": "Bearer adw_free_demo"
      }
    }
  }
}
```

Then, from your agent:

1. **Discover** — call `adw.catalog` with no arguments to list every available index (id, name, domain, grain, tiers, and the exact tool name to call).
2. **Query** — call the tool for the index you want, e.g. `adw.adw_001`, and get back a decision-grade score with drivers and provenance.
3. **Go deeper** — swap the demo token for your own free key at https://api.alpinedataworks.com/signup for higher limits and Gold/Platinum indices.

> Start with `adw.catalog` — it's the map to everything else.

---

## What you get

Each intelligence object is a **0–100 decision score** with its underlying drivers, confidence, freshness, and source lineage — built to be consumed by an agent, not eyeballed on a dashboard.

**Coverage spans three domains:**

| Domain | Examples |
|---|---|
| **Economic** | Inflation pressure, labor-market slack, consumer wallet-share, category pricing-power, recession-risk composites |
| **Market** | Flow and positioning signals, ETF share-count drift, momentum/reversal composites |
| **Geo-health** | Health-risk index for any US ZIP/ZCTA (CDC PLACES, 40 measures), cancer, water, mortality, SDOH, obesity, and air-quality layers across 32,520 ZCTAs, 29,923 cities, and 3,143 counties |

Representative tools:

- **`adw.catalog`** — list all available indices (call this first).
- **`adw.health_risk`** — geographic health-risk index for any US ZIP/ZCTA.
- **`adw.adw_NNN`** — query a specific intelligence index by id.
- **`adw.feedback`** — tell us how consumable the data is (built for agents to answer).

---

## Access & pricing

- **Free** — demo token (`adw_free_demo`) and a free account key for evaluation and Free-tier indices.
- **Gold — $199/mo** and **Platinum — $499/mo** — full catalog, higher limits, premium indices.

Discovery (`adw.catalog`) is **keyless** — agents can always see what's available before authenticating.

---

## How it's built

Every index is computed from **free, keyless, publicly reachable** data sources and passes a validation gate (each metric must beat a naive baseline before it becomes sellable). The server runs on Cloudflare's global edge; data is refreshed on scheduled compute cycles with self-healing history backfill.

- **Transport:** Streamable HTTP
- **Auth:** Bearer token (`Authorization: Bearer <key>`)
- **Manifest:** [`server.json`](./server.json) · live at [`/.well-known/mcp.json`](https://api.alpinedataworks.com/.well-known/mcp.json)
- **OpenAPI:** https://api.alpinedataworks.com/openapi.json

---

## Links

- **Home:** https://alpinedataworks.com
- **Catalog:** https://alpinedataworks.com/catalog
- **Sign up (free key):** https://api.alpinedataworks.com/signup
- **Console & docs:** https://api.alpinedataworks.com/console
- **Status:** https://alpinedataworks.com/status
- **Official MCP Registry:** `com.alpinedataworks/adw-intelligence-server`

---

_Maintained by AlpineDataWorks. Questions or issues → support@alpinedataworks.com_
