---
name: product.search
description: Search Amazon or eBay for products and get their info (title, price, rating, link). Calls the Zedkr API.
metadata:
  type: skill
  layer: discovery
  api: true
---

# product.search

Search Amazon or eBay for products and get their details. Give it what to look
for, the shipping country, and which store; get back a **list of options** with
prices and links.

**Do not auto-buy.** This skill only returns choices. Show the options to the user
and let **the user pick one**. You then take that product's `product_url` and pass
it to `order.place`. A `product_url` is required to place an order - nothing is
ordered until the user has chosen a specific product.

## Endpoint
```
POST {API_BASE}/v1/search
```

## Input
```json
{ "query": "iPhone 16 Pro Max 256GB", "country": "United States", "retailer": "amazon", "max_results": 20 }
```
- `query` - what to search for.
- `country` - where it ships (the API picks the right Amazon/eBay store automatically).
- `retailer` - `amazon` (default) or `ebay`.
- `max_results` - optional, default 20.

## Output
```json
{
  "retailer": "amazon",
  "marketplace_tld": "amazon.com",
  "count": 2,
  "products": [
    { "retailer": "amazon", "title": "Apple iPhone 16 Pro Max 256GB",
      "price": 1199.00, "rating": 4.8, "reviews": 1423,
      "product_url": "https://amazon.com/dp/B0DGHYDZ89" }
  ]
}
```

For eBay, set `retailer: "ebay"` - results carry eBay item links (no Prime; the
seller sets shipping). Each product has a ready-to-use `product_url`.

## Next
`order.place` - pass the `product_url` of the option the user chose (Amazon or eBay).
