---
name: greif-find-facilities
description: Find Greif facility and distributor locations, and the markets and technologies behind them, from the corporate site's WordPress REST API.
api: Greif WordPress REST API
base_url: https://www.greif.com/wp-json
generated: '2026-09-12'
method: generated
source: openapi/greif-wordpress-rest-openapi.yml
operations:
  - getWpV2WpslStores
  - getWpV2WpslStoresById
  - getWpV2StoreLocation
  - getWpV2WpslStoreCategory
  - getWpV2Market
  - getWpV2Technology
  - getWpV2Search
---

# Find Greif facilities, markets and technologies

Anonymous reads against `https://www.greif.com/wp-json`. No key, no account.

## Steps

1. **Get the region vocabulary.** `GET /wp/v2/store_location` (`getWpV2StoreLocation`) returns the
   region terms used to group facilities (APAC was observed with 20 members on 2026-09-12). Facility
   categories come from `GET /wp/v2/wpsl_store_category` (`getWpV2WpslStoreCategory`).

2. **List facilities.** `GET /wp/v2/wpsl_stores` (`getWpV2WpslStores`), narrowed with
   `?store_location=<term id>` or `?wpsl_store_category=<term id>`. Use `_fields` to trim, and page
   with `page`/`per_page` (max 100) while reading `X-WP-Total`.

3. **Fetch one facility.** `GET /wp/v2/wpsl_stores/{id}` (`getWpV2WpslStoresById`).

4. **Add context.** `GET /wp/v2/market` (`getWpV2Market`) lists the end markets Greif serves (14
   observed); `GET /wp/v2/technology` (`getWpV2Technology`) lists packaging technology pages. Both are
   editorial pages, so read `title.rendered` and `link`, and expect HTML in `content.rendered`.

5. **Fall back to search.** `GET /wp/v2/search?search=<term>` (`getWpV2Search`) spans all public
   content types and returns `id`, `title`, `url`, `type` and `subtype` — useful when you do not know
   which entity holds what you want.

## Do not

- Do not read `/wp/v2/sales-contact` or `/wp/v2/leadership-council` into any stored output. They answer
  anonymously but carry personal data.
- Do not attempt writes. Every mutating route requires a WordPress account on greif.com; anonymous
  callers get HTTP 401 `rest_forbidden`.
- Do not treat the locator as authoritative plant data — it is the website's locator content, and Greif
  publishes no freshness guarantee for it.
