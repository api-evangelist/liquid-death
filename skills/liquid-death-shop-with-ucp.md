---
name: Shop Liquid Death over UCP/MCP
description: Search, cart and check out at the Liquid Death store over the Universal Commerce Protocol MCP transport, with the agent identity, idempotency and human-approval rules the store requires.
api: mcp/liquid-death-mcp.yml
operations: [search_catalog, lookup_catalog, get_product, create_cart, update_cart, get_cart, create_checkout, update_checkout, complete_checkout, get_order]
generated: '2026-08-01'
method: generated
source: https://liquiddeath.com/agents.md
---

# Shop Liquid Death over UCP/MCP

Liquid Death exposes every transactional capability over one MCP endpoint implementing the Universal
Commerce Protocol. This is the **only** sanctioned way for an agent to transact with the store.

## Endpoints

- **Discovery**: `GET https://liquiddeath.com/.well-known/ucp`
- **MCP**: `POST https://liquiddeath.myshopify.com/api/ucp/mcp`
  (`Content-Type: application/json`, `Accept: application/json, text/event-stream`)

Current protocol version `2026-04-08`; `2026-01-23` is also supported but negotiates a smaller
capability set (fulfillment and the Shopify catalog extension both require `min: 2026-04-08`).

## Before you start — the two hard requirements

1. **Agent identity.** Every call must carry a `UCP-Agent` header (JSON-RPC `meta.ucp-agent.profile`)
   holding a resolvable URL to *your platform's* UCP profile document. Without it the server answers
   HTTP 422 with JSON-RPC `-32001` / `invalid_profile_url`. There is no anonymous mode.

2. **Human approval before payment.** `complete_checkout` moves money and the store forbids
   completing it without explicit, contemporaneous buyer consent — stated in both `/agents.md` and
   `robots.txt`. If you cannot obtain that approval at the moment of payment, do not proceed; the store
   directs you to route the purchase through Shop Pay via `https://shop.app/SKILL.md` instead.

## Steps

1. **Discover** — `GET /.well-known/ucp`. Confirm `services["dev.ucp.shopping"]` still lists a `mcp`
   transport, read the `endpoint`, and check the version you intend to speak is in
   `supported_versions`. Do not hardcode the endpoint; the profile is the source of truth.

2. **Search** — call `search_catalog` with the buyer's intent as query text. Always pass buyer context
   (`context.address_country`, `context.currency`); the store states pricing and availability are
   inaccurate without it.

3. **Resolve** — call `lookup_catalog` for a batch of identifiers, or `get_product` for one product's
   full detail, before you commit anything to a cart.

4. **Cart** — `create_cart`, then `update_cart` to adjust lines, `get_cart` to re-read.
   `cancel_cart` **requires** an `Idempotency-Key`.

5. **Checkout** — `create_checkout`, then `update_checkout` to set the shipping address and method.
   This merchant's fulfillment capability is single-destination
   (`allows_multi_destination.shipping: false`) and the only method combination it accepts is
   `[shipping]` — do not attempt split shipments or pickup+ship combinations.

6. **Complete** — obtain buyer approval, then call `complete_checkout` with a fresh UUID
   `Idempotency-Key` (mandatory). Available payment handlers: Shop Pay, Shopify card
   (visa, master, american_express, discover, diners_club) and Google Pay
   (VISA, MASTERCARD, AMEX, DISCOVER).

7. **Track** — `get_order` returns order state. Customer-scoped access rides the store's OIDC provider
   at `account.liquiddeath.com`; the relevant scope is `customer-account-mcp-api:full`.

## Idempotency

`Idempotency-Key` (UUID, mapped from `meta.idempotency-key`) is **required** on `complete_checkout`,
`cancel_checkout` and `cancel_cart`, and accepted on every other tool. Generate one key per logical
intent and **reuse the same key on retry** — never mint a new key when retrying, or you risk a
duplicate order.

## Error handling

- `429` — the endpoint is rate-limited per IP. Back off exponentially.
- `-32001 invalid_profile_url` — your agent profile is missing or unresolvable. Fix the `UCP-Agent`
  header, then retry.
- Every UCP error may carry `error.data.continue_url` — a human-completable URL. When you are blocked,
  hand that URL to the buyer rather than dead-ending.

## Never do

- Never complete checkout, payment or order placement without contemporaneous human approval.
- Never fall back to browser automation or scripted form fills against `/checkout` — explicitly
  forbidden in `robots.txt`.
- Never scrape `/cart.js`; use `create_cart`/`update_cart`.

## Related

- MCP manifest: `mcp/liquid-death-mcp.yml`
- REST/MCP divergence: `mcp/liquid-death-tool-crosswalk.yml`
- Conventions: `conventions/liquid-death-conventions.yml`
- Authentication: `authentication/liquid-death-authentication.yml`
