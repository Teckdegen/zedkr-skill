# Zedkr Commerce Agent - Flow

Zedkr lets an AI agent buy real Amazon and eBay products and pay with USDC on Pharos. Two
skills, both markdown that call the hosted **Zedkr API** (`{API_BASE}`). The agent
threads one step's output into the next; everything else is handled for you.

The user sees only: **what they want → a Pharos address + amount → "ordered."**

## Flow

```
Search → Place order → (user sends USDC on Pharos) → Paid → Ships
```

| Step | Skill                   | Endpoint                       | In → Out                                            |
|------|-------------------------|--------------------------------|-----------------------------------------------------|
| 1    | product.search   | POST /v1/search                | query, country → products[] (price, link)           |
| 2    | order.place             | POST /v1/place-order           | product + address → price + Pharos wallet to pay    |
| 3    | order.place (poll)      | GET /v1/checkout/:id           | checkout_id → status until `paid`                   |

The shipping address and product from step 2 are saved, and the Amazon order is
placed automatically once the payment is confirmed in step 3.

## Example - "Buy iPhone 16 Pro Max" (ship to US)

```
1. product.search { query:"iPhone 16 Pro Max 256GB", country:"United States" }
     → products[2], pick product_url:"https://amazon.com/dp/B0DGHYDZ89"
2. order.place { product_url:"https://amazon.com/dp/B0DGHYDZ89", quantity:1,
                 customer_email:"...", shipping_address:{...,phone:"+1..."} }
     → total_amount:1199.00,
       wallet:{ funding_address:"0x…", network:"pharos", amount:1199.00 }
   → user sends 1199 USDC on Pharos to 0x…
3. GET /v1/checkout/chk_123  [poll every ~10s]
     → { status:"paid", order_number:"ORD-…" }  → order ships
```

## Notes
- Each skill is markdown referencing one API endpoint - no inline code.
- The right Amazon/eBay store is auto-selected from the shipping country (`retailer: amazon|ebay`).
- No API key needed; money only moves once the user funds their own Pharos wallet.
- The order number is assigned when payment is confirmed (returned by step 3).
