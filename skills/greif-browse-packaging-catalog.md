---
name: greif-browse-packaging-catalog
description: Read Greif's packaging product catalog and its classification taxonomies as JSON from the corporate site's WordPress REST API, filtering by category, material, capacity, market or regional availability.
api: Greif WordPress REST API
base_url: https://www.greif.com/wp-json
generated: '2026-09-12'
method: generated
source: openapi/greif-wordpress-rest-openapi.yml
operations:
  - getWpV2Product
  - getWpV2ProductById
  - getWpV2ProductCategory
  - getWpV2ProductMaterial
  - getWpV2ProductCapacity
  - getWpV2ProductAttribute
  - getWpV2ProductMarket
  - getWpV2RegionalAvailability
---

# Browse the Greif packaging catalog

Greif publishes no developer program. This skill works against the WordPress REST API its corporate
site serves at `https://www.greif.com/wp-json`, which is the only machine-readable contract Greif
exposes. Reads are anonymous; nothing here needs a key.

Treat this surface as incidental, not contractual: every response carries `X-Robots-Tag: noindex`,
there is no changelog, and routes can disappear when a plugin changes.

## Steps

1. **Learn the vocabulary before filtering.** Fetch the taxonomies you intend to filter on and keep the
   term `id` values:
   - `GET /wp/v2/product_category` (`getWpV2ProductCategory`) — product families
   - `GET /wp/v2/product_material` (`getWpV2ProductMaterial`) — steel, plastic, fibre, adhesives
   - `GET /wp/v2/product_capacity` (`getWpV2ProductCapacity`)
   - `GET /wp/v2/product_market` (`getWpV2ProductMarket`) — end markets
   - `GET /wp/v2/product_attribute` (`getWpV2ProductAttribute`)
   - `GET /wp/v2/regional_availability` (`getWpV2RegionalAvailability`)
   Each term carries `id`, `name`, `slug`, `count` and `link`. Filter by `id`, never by name.

2. **List products.** `GET /wp/v2/product` (`getWpV2Product`). Pass taxonomy ids to narrow, e.g.
   `?product_material=<id>&product_market=<id>`. Use `_fields=id,slug,title,link` to keep responses
   small and `_embed` when you want the linked terms inline.

3. **Page correctly.** `per_page` defaults to 10 and is capped at **100** — asking for more returns
   HTTP 400 `rest_invalid_param` with `data.details.per_page.code = rest_out_of_bounds`. Read
   `X-WP-Total` and `X-WP-TotalPages` from the response headers, or follow the RFC 8288
   `Link: …; rel="next"` header, rather than guessing when to stop.

4. **Fetch one product.** `GET /wp/v2/product/{id}` (`getWpV2ProductById`). An unknown id returns
   HTTP 404 `rest_post_invalid_id`.

## What this surface will not give you

Price, stock, lead time and datasheets are **not** here — they live in the authenticated Greif+ portal
at `https://plus.greif.com`. Only a subset of the catalog is REST-registered (10 product records were
observed on 2026-09-12), so do not present these results as Greif's complete product line.

## Errors

The envelope is WordPress's `{code, message, data:{status}}`, not RFC 9457 problem+json. See
`errors/greif-problem-types.yml` for the five codes observed live.
