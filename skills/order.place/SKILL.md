---
name: order.place
description: Place an Amazon or eBay order from a product link. Returns price, order ID, and a Pharos USDC wallet for the buyer to fund.
metadata:
  type: skill
---

# Place Order

Lock in **one specific product** the user chose, get the exact USDC amount, and
receive a one-time wallet address for payment.

## When to use

Only after the **user has given a specific product**. You must have a concrete
Amazon or eBay `product_url` - a direct link the user provided. A `product_url` is
**required**; the order is locked in to that exact item. Never guess or auto-pick.

## Step 1 - Place order

```
POST {API_BASE}/v1/place-order
Content-Type: application/json
```

```json
{
  "product_url": "https://amazon.com/dp/B0DGHYDZ89",
  "quantity": 1,
  "customer_email": "buyer@example.com",
  "shipping_address": {
    "name": "John Doe",
    "address1": "123 Main St",
    "city": "San Francisco",
    "state": "CA",
    "postalCode": "94105",
    "zip": "94105",
    "country": "United States",
    "phone": "+14155551234"
  }
}
```

`customer_email` and `shipping_address.phone` are required. The product, email, and
shipping address are stored server-side and used to place the Amazon order only
once payment is confirmed.

## Response

```json
{
  "marketplace_tld": "amazon.com",
  "total_amount": 1199.00,
  "wallet": {
    "checkout_id": "chk_abc",
    "funding_address": "0x...",
    "network": "pharos",
    "asset": "USDC",
    "amount": 1199.00,
    "expires_at": "2026-06-14T12:30:00Z",
    "status": "awaiting_funds"
  }
}
```

Tell the buyer: send exactly `wallet.amount` USDC on Pharos to `wallet.funding_address`.
(The order number is assigned when payment is confirmed - see step 2.)

## Step 2 - Poll until paid

```
GET {API_BASE}/v1/checkout/{checkout_id}
```

Poll every ~10 seconds until `status` is `paid`. On confirmation the system places
the Amazon order using the stored address + product and the order ships.

```json
{ "checkout_id": "chk_abc", "status": "paid", "order_number": "ORD-1234567890" }
```

## Track orders

```
GET {API_BASE}/v1/orders
```
