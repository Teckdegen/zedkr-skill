# Zedkr

**Buy real products with an AI agent and pay in USDC on Pharos.**

Zedkr is an agent skill that turns stablecoins into physical products. You give an
agent a product link from Amazon or eBay and a shipping address. The agent pays in
USDC on Pharos, and the item is bought and shipped to the door. No browser wallet,
no card, no checkout page.

This repo is a **skill pack**: plain markdown an AI agent reads to learn how to call
the Zedkr API. There is no SDK and nothing to install. The agent reads `SKILL.md`
and makes plain HTTP requests. One skill: `order.place`.

## The skill: order.place

| Call | Purpose |
|------|---------|
| `POST {api_base}/v1/place-order` | quote a product link and get a wallet to pay |
| `GET {api_base}/v1/checkout/{id}` | report status until the order is placed |

`api_base` is set in [`manifest.json`](manifest.json), currently
`https://zedkr.up.railway.app`. Full request and response shapes live in
[`skills/order.place/SKILL.md`](skills/order.place/SKILL.md).

## What a call looks like

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
    "funding_address": "0x23f3cE44065f400E9818C19E126D5c044aFC6cdc",
    "network": "pharos",
    "asset": "USDC",
    "amount": 808,
    "status": "awaiting_funds"
  }
}
```

Send the `amount` of USDC on Pharos to `funding_address`, then poll the checkout:

```bash
curl https://zedkr.up.railway.app/v1/checkout/{checkout_id}
# status goes awaiting_funds, then paid
```

When `status` becomes `paid`, the order has been placed with the retailer.

## How it works

- **You provide the exact product** as a direct link, so there is never any
  guessing about what gets ordered.
- **Payment is USDC on Pharos.** Each order gets a fresh wallet used only once,
  and nothing is charged until you send funds to it.
- **The order is placed only after the payment is confirmed**, settled onchain
  through the x402 payment protocol on the backend.
- `customer_email` and `shipping_address.phone` are required by the retailer.
- One product per link. A quote expires after about 30 minutes; request a new one
  if it lapses.

## Notes

Built for the Pharos Skill to Agent hackathon. The backend runs separately and
handles the retailer integration and onchain settlement; this repo is only the
skill contract an agent needs to use it.
