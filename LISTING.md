# Listing checklist (for the owner)

How to list or update the AlpineDataWorks MCP server in public directories. Nothing here has been submitted or published yet. Each step is yours to run.

Checked against each directory's docs on 2026-11-19; counts and directory status refreshed 2026-11-22 from a live `tools/list` (319 tools) and `adw.catalog` (315 indices). Sources are at the bottom.

## 0. Before you start

- [x] The adw-api change is deployed: the live server already reports version `0.3.9` (checked 2026-11-22). Re-check:
  ```bash
  curl -s https://api.alpinedataworks.com/.well-known/mcp.json | grep -o '"version":"[^"]*"' | head -1
  ```
- [ ] Merge this repo's PR, then `git pull` on your laptop.
- [ ] Update this repo's GitHub description (it still says 163 tools / 155 indices):
  ```bash
  gh repo edit btabiado/adw-mcp \
    --description "Remote MCP server: 319 tools over 315 economic, market & US geo-health intelligence indices for AI agents." \
    --homepage https://alpinedataworks.com/docs \
    --add-topic mcp --add-topic mcp-server --add-topic model-context-protocol --add-topic economic-data --add-topic public-health
  ```

## 1. Official MCP Registry (update the existing listing to 0.3.9)

You are already listed as `com.alpinedataworks/adw-intelligence-server`. The newest published version is `0.3.8` (Oct 19). Published versions can't be changed, so this repo's `server.json` is now `0.3.9`, the same version the live server reports in its `initialize` handshake. `0.3.9` has never been published, so it is free to use. The registry entry still says "317 MCP tools, 309 driver-backed indices"; `0.3.9` says 315 indices and 319 tools, which is what the live server serves.

Your login uses **DNS verification**, and the TXT record is already live on `alpinedataworks.com` (`v=MCPv1; k=ed25519; p=NI2e9YCl…`). You need the `key.pem` you made in July. Don't switch to GitHub login: that only allows `io.github.btabiado/…` names, which would create a second, separate listing. No `/.well-known` file is needed on the API host, because the DNS record already covers it.

1. Install the publisher:
   ```bash
   brew install mcp-publisher
   mcp-publisher --help
   ```
2. Log in with your existing key. On a Mac, use Homebrew's OpenSSL 3 (`brew install openssl@3`), because the built-in LibreSSL can't read Ed25519 keys:
   ```bash
   OPENSSL=/opt/homebrew/opt/openssl@3/bin/openssl   # Intel Mac: /usr/local/opt/openssl@3/bin/openssl
   MCP_PRIVATE_KEY="$($OPENSSL pkey -in key.pem -noout -text | sed -n '/priv:/,/pub:/p' | sed '1d;$d' | tr -d ' :\n')" \
     mcp-publisher login dns --domain alpinedataworks.com
   ```
   If you have lost `key.pem`, make a new key and **add** a second TXT record. Keep the old record until the login works:
   ```bash
   $OPENSSL genpkey -algorithm Ed25519 -out key.pem
   echo "v=MCPv1; k=ed25519; p=$($OPENSSL pkey -in key.pem -pubout -outform DER | tail -c 32 | base64)"
   # Cloudflare DNS → alpinedataworks.com → add TXT record, name "@", content = the line printed above
   ```
   Keep `key.pem` private and never commit it.
3. Validate, then publish:
   ```bash
   cd adw-mcp
   mcp-publisher validate
   mcp-publisher publish
   ```
4. Check that `0.3.9` is now the latest version:
   ```bash
   curl -s "https://registry.modelcontextprotocol.io/v0/servers?search=com.alpinedataworks" | grep -o '"version":"[^"]*"'
   ```
5. For every later release: bump `version` in `server.json` **and** `MCP_SERVER_VERSION` in adw-api's `src/mcp.ts` (plus `API_VERSION` in `wrangler.toml`). Deploy, then publish again.

## 2. Claude connectors directory (claude.ai)

**Blocked until OAuth is on.** The directory expects OAuth 2.0 (or no login) for services that need an account. API-key headers are a limited beta that most Claude organizations can't use. The adw-api file `CLAUDE_DIRECTORY_SUBMISSION.md`, section 3, lists what turning OAuth on needs. Your plan is not a blocker any more: any paid plan (Pro, Max, Ultra, Team, Enterprise) can submit.

When OAuth is live:

1. In Claude, add `https://api.alpinedataworks.com/mcp` as a custom connector (Settings → Connectors). Sign in, and call a few tools, including `adw.catalog`, `adw.health_risk` and `adw.alerts`.
2. Open https://claude.ai/directory/manage → **Submit new** → **MCP connector**.
3. Fill in the steps with the text below.

| Portal step | What to enter |
|---|---|
| **1. Connection** | `https://api.alpinedataworks.com/mcp`, **Universal URL** |
| **2. Tools** | Syncs automatically. Expect 317 read-only and 2 write (`adw.feedback`, `adw.alerts`), with no missing-annotation flags |
| **3. Listing: name** | `AlpineDataWorks Intelligence` |
| **3. Listing: one-liner** (≤200) | `Decision-grade economic, market and US geo-health intelligence: 315 scored indices with drivers, confidence and sources, ready for Claude to query.` |
| **3. Listing: description** (≤2,000) | The long description below |
| **3. Listing: categories** (1–5) | Pick the closest offered: Data & Analytics, Finance, Health, Research |
| **3. Listing: documentation URL** | `https://alpinedataworks.com/docs` |
| **3. Listing: privacy policy URL** | `https://alpinedataworks.com/legal/privacy` |
| **3. Listing: support contact** | `support@alpinedataworks.com` |
| **3. Listing: icon** | Download `https://alpinedataworks.com/favicon.png` (256×256 PNG) and upload it |
| **3. Listing: slug** (permanent) | `alpinedataworks` |
| **4. Use cases** | See below. Prerequisites: `A free AlpineDataWorks account (sign up at https://api.alpinedataworks.com/signup). Free accounts get 1,000 credits; more credits are pay-as-you-go.` Reads/writes: **Both**. Almost every tool reads; `adw.alerts` manages your own alert follows and `adw.feedback` sends feedback |
| **5. Company** | `AlpineDataWorks.AI`, `https://alpinedataworks.com`, your name and email as the primary contact |
| **6. Authentication** | **Always required** (or **Required when the server asks** if you keep `tools/list` and `adw.sample` open), with **OAuth sign-in** using Dynamic Client Registration |
| **7. Data handling** | API: **our own (first-party)**. Personal health data: **No**. Geo-health tools return aggregate, public statistics by ZIP or county, never data about a person. Sponsored content: **No** |
| **8. Test & launch** | Confirm you ran every tool. Never put credentials in the portal; reviewers ask by email |
| **9. Compliance** | Tick all seven. The server calls only our own API, moves no money or crypto, generates no AI media, has no prompt-injection text, collects no conversation data, and has public docs |

**Long description (paste as is):**

> AlpineDataWorks turns public, authoritative data into decision-grade intelligence that Claude can query directly. Each of the 315 indices returns a 0–100 score with its top drivers, confidence, freshness and source lineage, so Claude can explain why a number moved rather than just quoting it.
>
> Coverage spans three domains:
> • Economic: inflation pressure, labor-market slack, consumer wallet-share, pricing power and recession-risk composites.
> • Market: flow and positioning signals, ETF share-count drift, momentum and reversal composites, and crypto market indices.
> • US geo-health: a health-risk index for any ZIP code (40 CDC PLACES measures) plus county-level cancer, drinking water, mortality, social determinants, obesity and air quality, covering 32,520 ZCTAs, 29,923 cities and 3,143 counties.
>
> Sources include FRED, Census, BLS, BEA, EIA, USGS, NOAA, openFDA, CDC and EPA. Every index must beat a naive baseline before it is listed.
>
> Start with the catalog tool to see every index and the tool that serves it. Data tools are read-only. Two small tools write: one lets you follow or unfollow score alerts on your own account, and one sends feedback.
>
> A free account includes 1,000 credits (most calls cost 3 credits). More credits are pay-as-you-go and never expire. Results are informational only and are not financial, medical or investment advice.

**Use cases (paste as is):**

> 1. Macro and market briefings: ask Claude for today's inflation, labor or recession-risk read, with the drivers behind each score.
> 2. Location health research: compare health risk, drinking-water violations or air quality across ZIP codes or counties for site selection, grant writing or reporting.
> 3. Monitoring: follow the indices you care about and get one email digest a day when a score moves.

After you submit, Anthropic scans the submission and usually lists it as a **Community** connector. Track it at https://claude.ai/directory/manage. For escalations, email `mcp-review@anthropic.com`.

## 3. Glama

`glama.json` is already in this repo (`maintainers: ["btabiado"]`), and Glama also picks up servers from the Official MCP Registry.

- [ ] Sign in at https://glama.ai with GitHub and find "AlpineDataWorks". If it isn't claimed, use **Claim ownership**. If it is, re-run the claim or refresh flow so Glama picks up the new README and version.
- [ ] On the listing, set the `Authorization: Bearer adw_free_demo` header for inspection, so Glama's checks can call tools.
- Today the listing is unclaimed and graded **B (3.3/5)**. It loses points for numeric tool names (`adw_adw_591` and so on), overlapping tools and tool count. Claiming it lets you edit the listing and re-run the checks after the registry update.

## 4. Smithery

Smithery shut down its service on 2026-10-31. Nothing to do.

## 5. PulseMCP (refresh the stale description)

You are listed as "AlpineDataWorks Intelligence" (since 5 Jul 2026), but the description still says "155+". PulseMCP's new-server submissions are paused, and it syncs from the Official MCP Registry, so step 1 should refresh it. If it still says "155+" a week after step 1, ask them to refresh it through https://www.pulsemcp.com/submit or by email to hello@pulsemcp.com.

## 6. mcp.so

- [ ] Go to https://mcp.so/submit, sign in, and pick **Remote Server**. Use the free submission (the $39 paid option is optional). Paste:
  - Name: `AlpineDataWorks Intelligence`
  - URL: `https://github.com/btabiado/adw-mcp`
  - Server: `https://api.alpinedataworks.com/mcp` (Streamable HTTP, `Authorization: Bearer adw_free_demo`)
  - Description: the one-liner from step 2

## Reusable text

- **Name**: AlpineDataWorks Intelligence
- **Short description (≤100, registry)**: Economic, market & US geo-health intelligence for AI agents: 315 scored indices as 319 MCP tools.
- **One-liner (≤200)**: Decision-grade economic, market and US geo-health intelligence: 315 scored indices with drivers, confidence and sources, ready for Claude to query.
- **Endpoint**: https://api.alpinedataworks.com/mcp (Streamable HTTP)
- **Auth**: `Authorization: Bearer <key>`; demo `adw_free_demo`; free key at https://api.alpinedataworks.com/signup
- **Logo**: https://alpinedataworks.com/favicon.png (256×256 PNG); social card https://alpinedataworks.com/og.png (1200×630)
- **Website / docs**: https://alpinedataworks.com · https://alpinedataworks.com/docs
- **Pricing**: https://alpinedataworks.com/pricing
- **Privacy / terms / DPA**: https://alpinedataworks.com/legal/privacy · https://alpinedataworks.com/legal/terms · https://alpinedataworks.com/legal/dpa
- **Support**: support@alpinedataworks.com · **Security**: security@alpinedataworks.com

## Sources

- MCP Registry publishing quickstart: https://modelcontextprotocol.io/registry/quickstart
- MCP Registry authentication (GitHub, DNS, HTTP): https://modelcontextprotocol.io/registry/authentication
- MCP Registry remote servers: https://modelcontextprotocol.io/registry/remote-servers
- MCP Registry versioning: https://modelcontextprotocol.io/registry/versioning
- Publisher CLI reference: https://github.com/modelcontextprotocol/registry/blob/main/docs/reference/cli/commands.md
- server.json schema (2025-12-11): https://static.modelcontextprotocol.io/schemas/2025-12-11/server.schema.json
- Claude: submit a connector: https://claude.com/docs/connectors/building/submission
- Claude: connector review criteria: https://claude.com/docs/connectors/building/review-criteria
- Claude: authentication for connectors: https://claude.com/docs/connectors/building/authentication
- Claude: who can submit: https://claude.com/docs/directory/publish
- Glama, glama.json: https://glama.ai/blog/2025-07-08-what-is-glamajson
- PulseMCP submissions: https://www.pulsemcp.com/submit
