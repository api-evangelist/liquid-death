# Liquid Death

Liquid Death is a Los Angeles beverage company (founded 2014; still and sparkling mountain water, iced
tea, flavored sparkling water, Death Dust electrolyte powder, and a Sparkling Energy line) selling
direct-to-consumer at [liquiddeath.com](https://liquiddeath.com/) alongside retail distribution.

It has no developer portal and no published OpenAPI — but it is one of the more agent-forward
storefronts in the network. On its own host it serves:

- **`/agents.md`** — a first-party agent instruction document, mirrored verbatim at **`/llms.txt`**, and
  announced through a dedicated `sitemap_agentic_discovery.xml`.
- **`robots.txt` as agent policy** — names the UCP endpoints, routes agents off the AJAX cart surfaces,
  and states the buyer-approval invariant ("Checkouts are for humans").
- **`/.well-known/ucp`** — a Universal Commerce Protocol merchant profile advertising protocol versions
  `2026-04-08` and `2026-01-23`, eight negotiated capabilities, and three payment handlers.
- **A live UCP shopping MCP endpoint** at `https://liquiddeath.myshopify.com/api/ucp/mcp` — 13 tools,
  with `Idempotency-Key` **required** on `complete_checkout`, `cancel_checkout` and `cancel_cart`.
- **Both** `/.well-known/openid-configuration` **and** `/.well-known/oauth-authorization-server` for the
  customer account at `account.liquiddeath.com`.

The store runs a deliberate read/write split: the product and collection JSON surface is anonymous and
read-only, while every transactional capability is confined to the identity-gated MCP transport. See
`mcp/liquid-death-tool-crosswalk.yml` — 3 of 13 tools have a REST analogue, 10 are MCP-only.

## Artifacts

| Path | What it is |
|---|---|
| `openapi/liquid-death-storefront-openapi.yml` | OpenAPI 3.1 for the read-only storefront JSON surface. **Generated** by API Evangelist from the endpoints the provider documents in `/agents.md`; every operation probed live 2026-08-01. Not published by Liquid Death. |
| `overlays/liquid-death-storefront-overlay.yaml` | Overlay 1.0.0 of our enhancements (agent policy, rate limits, pagination) |
| `mcp/liquid-death-mcp.yml` | The UCP shopping MCP server manifest |
| `mcp/liquid-death-ucp-shopping-mcp.openrpc.json` | The canonical UCP Shopping OpenRPC document the merchant's own profile declares as its MCP schema (verbatim) |
| `mcp/liquid-death-tool-crosswalk.yml` | MCP tool ↔ REST operation binding, plus the MCP-only / REST-only divergences |
| `llms/liquid-death-llms.txt` | `/llms.txt`, verbatim |
| `well-known/` | UCP profile, OIDC + RFC 8414 metadata, robots.txt, and the probe index |
| `authentication/`, `scopes/` | Anonymous / UCP agent-profile / customer OIDC model and the four advertised scopes |
| `conventions/liquid-death-conventions.yml` | Idempotency, pagination, error envelopes, rate limiting, buyer context, human-in-the-loop |
| `errors/liquid-death-problem-types.yml` | Observed errors on both surfaces, including the UCP `continue_url` recovery affordance |
| `conformance/`, `lifecycle/`, `data-model/` | Standards conformance, UCP version support window, entity graph |
| `security/liquid-death-domain-security.yml` | Probed TLS/HSTS/DNSSEC/CAA/SPF/DMARC |
| `agentic-access/`, `skills/` | Generated `x-agentic-access` contracts and two packaged agent skills |

## Not present

No A2A agent card (`/.well-known/agent-card.json` and `/.well-known/agent.json` both 404 on
`liquiddeath.com`, `liquiddeath.myshopify.com` and `account.liquiddeath.com`), no `security.txt`, no
vulnerability disclosure program, no trust center, no status page, no changelog, no deprecation policy,
no first-party SDK, CLI or GitHub organization, and no event/webhook surface.

- Website: https://liquiddeath.com/
- Agent instructions: https://liquiddeath.com/agents.md
- Secondary market: https://forgeglobal.com/liquid-death_stock/
