---
name: Shop BloomThis via UCP agent commerce
description: Discover flowers and gifts and run a buyer-approved checkout against the BloomThis Shopify store using its published Universal Commerce Protocol (UCP) MCP endpoint.
api: mcp/bloomthis-mcp.yml
method: generated
source: https://bloomthis.co/llms.txt, https://bloomthis.co/.well-known/ucp
operations: [search_catalog, create_cart, create_checkout, update_checkout, complete_checkout]
---

# Shop BloomThis via UCP agent commerce

BloomThis (https://bloomthis.co) is a Malaysian florist running on Shopify with a
native, hosted MCP endpoint exposed through the Universal Commerce Protocol (UCP).
Use it to shop and transact on the buyer's behalf. Every tool name below is documented
in the store's `/llms.txt`; do not invent tools.

## Endpoint

- MCP endpoint: `POST https://bloomthis.myshopify.com/api/ucp/mcp` (`Content-Type: application/json`, JSON-RPC MCP)
- Discovery: `GET https://bloomthis.co/.well-known/ucp`
- Supported UCP versions: `2026-04-08` (latest), `2026-01-23`

## Rules (non-negotiable)

- **Payment requires contemporaneous human approval.** Never call `complete_checkout`
  without explicit buyer consent at the moment of payment. If you cannot get it, route
  the purchase through the Shop skill (https://shop.app/SKILL.md) / Shop Pay instead.
- **Back off on HTTP 429.** The endpoint is per-IP rate-limited.
- **Pass buyer context** — `context.address_country` and `context.currency` — for
  accurate pricing and availability (this store delivers in Malaysia: KL, Selangor,
  Kedah, Penang, Negeri Sembilan, Johor Bahru).

## Steps

1. **Discover** — `GET /.well-known/ucp` to confirm the store's capabilities and MCP endpoint.
2. **Search** — `search_catalog` to find products matching the buyer's intent (e.g. a
   flower box or bouquet for a specific occasion / delivery date).
3. **Cart** — `create_cart` to add the chosen item(s).
4. **Checkout** — `create_checkout` to start the purchase flow from the cart.
5. **Fulfill** — `update_checkout` to set the recipient shipping address and delivery
   method (this store ships to a single destination per checkout).
6. **Complete** — `complete_checkout` only after the buyer explicitly approves payment.

## Read-only browsing (no auth)

For price/product lookups without transacting, use the storefront JSON surface:
`GET /products/{handle}.json`, `GET /collections/{handle}/products.json`,
`GET /search?q={query}&type=product`.

## Related

- Auth model: `authentication/bloomthis-authentication.yml`
- Conventions (versioning, rate limits, consent): `conventions/bloomthis-conventions.yml`
- MCP server manifest: `mcp/bloomthis-mcp.yml`
