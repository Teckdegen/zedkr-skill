# Zedkr Commerce Agent - Flow

Zedkr lets an AI agent buy a real Amazon or eBay product and pay with USDC on
Pharos. One skill, markdown that calls the hosted **Zedkr API** (`{API_BASE}`).
The caller supplies a product link; everything else is handled for you.

The user sees only: **a product link + address → a Pharos address + amount → "ordered."**

## Flow

```
Give product link → Place order → (user sends USDC on Pharos) → Paid → Ships
```

| Step | Skill              | Endpoint              | In → Out                                          |
|------|--------------------|-----------------------|---------------------------------------------------|
| 1    | order.place        | POST /v1/place-order  | product_url + address → price + Pharos wallet to pay |
| 2    | order.place (poll) | GET /v1/checkout/:id  | checkout_id → status until `paid`                 |

The shipping address and product from step 1 are saved, and the order is placed
automatically once the payment is confirmed in step 2.

## Example - buy a specific product (ship to US)

```
1. order.place { product_url:"https://amazon.com/dp/B0DGHYDZ89", quantity:1,
                 customer_email:"...", shipping_address:{...,phone:"+1..."} }
     → total_amount:1199.00,
       wallet:{ funding_address:"0x…", network:"pharos", amount:1199.00 }
   → user sends 1199 USDC on Pharos to 0x…
2. GET /v1/checkout/chk_123  [poll every ~10s]
     → { status:"paid", order_number:"ORD-…" }  → order ships
```

## Notes
- The skill is markdown referencing one API endpoint - no inline code.
- The caller provides a direct Amazon or eBay product URL (no product search).
- No API key needed; money only moves once the user funds their own Pharos wallet.
- The order number is assigned when payment is confirmed (returned by step 2).
