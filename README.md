# Zedkr

An agent skill for buying physical products with stablecoin.

Give it an Amazon or eBay product link and a shipping address. It quotes the price
and hands back a one-time Pharos wallet. Fund that wallet with USDC and the order
is placed with the retailer and shipped to the address.

This repo is a **skill pack** — markdown an AI agent reads to learn how to call the
Zedkr API. There's no SDK and nothing to install; the agent reads `SKILL.md` and
makes plain HTTP requests. One skill: `order.place`.

## The skill: `order.place`

| Call | Purpose |
|------|---------|
| `POST {api_base}/v1/place-order` | quote a product link + get a wallet to pay |
| `GET {api_base}/v1/checkout/{id}` | poll until the order is placed |

`api_base` is set in [`manifest.json`](manifest.json) — currently
`https://zedkr.up.railway.app`. Full request/response shapes are in
[`skills/order.place/SKILL.md`](skills/order.place/SKILL.md).

## What a call actually looks like

Place an order:

```bash
curl -X POST https://zedkr.up.railway.app/v1/place-order \
  -H 'content-type: application/json' \
  -d '{
    "product_url": "https://www.amazon.com/dp/B0DHJ896RY",
    "quantity": 1,
    "customer_email": "you@example.com",
    "shipping_address": {
      "name": "Jane Doe", "address1": "123 Main St", "city": "San Francisco",
      "state": "CA", "postalCode": "94105", "country": "United States",
      "phone": "+14155551234"
    }
  }'
```

Response:

```json
{
  "marketplace_tld": "amazon.com",
  "subtotal": 808,
  "shipping_amount": 0,
  "total_amount": 808,
  "wallet": {
    "checkout_id": "001ac29c-f1c1-4b99-b19f-520599b4f1e7",
    "funding_address": "0x0fF323d44AFE4cd9f47c98F6c8edafeF6C19280B",
    "network": "pharos",
    "asset": "USDC",
    "amount": 808,
    "expires_at": "2026-06-15T15:25:27Z",
    "status": "awaiting_funds"
  }
}
```

Send `808` USDC on Pharos to that address, then poll the checkout:

```bash
curl https://zedkr.up.railway.app/v1/checkout/001ac29c-f1c1-4b99-b19f-520599b4f1e7
# { "status": "awaiting_funds" }   ->   { "status": "paid", "order_number": "..." }
```

When `status` becomes `paid`, the order has been placed with the retailer.

## How it works

- **You provide the exact product** (a direct link), so there's no guessing — that
  item is what gets ordered.
- **Payment is USDC on Pharos.** Each order gets a fresh single-use address;
  nothing is charged until you send funds to it.
- **The order is placed only after payment is confirmed** — not before.
- `customer_email` and `shipping_address.phone` are required by the retailer.
- One product per link. A quote/wallet expires after ~30 minutes; request a new one
  if it lapses.

## Notes

Built for the Pharos Skill-to-Agent hackathon. The backend runs separately (it
handles the retailer integration and on-chain settlement); this repo is only the
skill contract an agent needs to use it.
