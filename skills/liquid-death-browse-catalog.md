---
name: Browse the Liquid Death catalog
description: Read Liquid Death product and collection data anonymously, without transacting, using the read-only storefront JSON surface the store publishes for agents.
api: openapi/liquid-death-storefront-openapi.yml
operations: [listCollectionProducts, getProduct, getSitemap]
generated: '2026-08-01'
method: generated
source: https://liquiddeath.com/agents.md
---

# Browse the Liquid Death catalog

Use this skill when you need product, price, or availability data from Liquid Death and you are **not**
buying anything. The store publishes this surface deliberately: `/agents.md` calls it "Read-Only
Browsing (No Authentication Required)".

## Before you start

- **No credential is required.** Do not attempt to authenticate for these calls.
- Base URL: `https://liquiddeath.com`
- If you need *structured search* or anything transactional, stop and use
  `liquid-death-shop-with-ucp.md` instead — this surface is read-only on purpose.

## Steps

1. **List the catalog** — call `listCollectionProducts` with `handle=all`:
   `GET /collections/all/products.json?limit=50&page=1`
   The response is `{"products": [...]}`. Pagination is offset-based via `limit` and `page`; there is
   no cursor and no total count, so page until you receive fewer items than `limit`.

2. **Narrow to a collection** — call `listCollectionProducts` with a real collection handle instead of
   `all`. Handles observed on the live sitemap include `water`, `liquid-death-mountain-water`,
   `merch-store`, `featured-merch`, `sweatshirts`, `hats-beanies`, `stickers-posters`.
   Discover more handles from `getSitemap` (`GET /sitemap.xml` → `sitemap_collections_1.xml`).

3. **Fetch one product** — call `getProduct`: `GET /products/{handle}.json`.
   Read `variants[]` for the purchasable units: `sku`, `price`, `price_currency`,
   `compare_at_price`, `option1`/`option2`/`option3`, `requires_shipping`, `quantity_rule` and
   `quantity_price_breaks`. Read `options[]` for the option axes (observed: `Flavors`, `Size`).

4. **Handle misses** — a handle that does not resolve returns `404` with a JSON body
   `{"errors": "..."}`, not an HTML page. Treat 404 as "not published", not as an outage.

## Rules

- **Do not** call `/cart.js` or `/recommendations/products`. Both are `Disallow`ed for agents in
  `robots.txt`, which instructs agents to use the UCP/MCP endpoints instead.
- **Do not** script `/checkout`, `/checkouts/`, `/orders` or `/account`. All are disallowed.
- `searchStorefront` (`GET /search?q=...&type=product`) returns **HTML**, not JSON. It is documented,
  but if you need structured results use the `search_catalog` MCP tool.
- Prices are per-variant and carry an explicit `price_currency`; do not assume USD from the storefront
  locale.

## Related

- Conventions: `conventions/liquid-death-conventions.yml`
- Errors: `errors/liquid-death-problem-types.yml`
- Data model: `data-model/liquid-death-data-model.yml`
