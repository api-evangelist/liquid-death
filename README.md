# Liquid Death

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
