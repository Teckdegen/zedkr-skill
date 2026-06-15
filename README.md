# Zedkr

**We buy Amazon & eBay products for you - pay with USDC on Pharos.**

Zedkr lets an AI agent buy a product from Amazon or eBay on your behalf. You give
it a product link, it places the order, and you pay with USDC on Pharos. The
product ships to your door. No accounts, no card, no checkout pages - just an
address to send USDC to.

## How it works

```
1. Pick          You give the agent an Amazon or eBay product link.
2. Place order   Give a shipping address - Zedkr locks in that product's price and
                 hands back a Pharos wallet address + the exact USDC amount.
3. Pay           Send that USDC on Pharos to the address.
4. Done          Once it's received, Zedkr buys that item and it ships to you.
```

You choose the exact product (a direct link); that exact item is what gets ordered.
Pay in USDC, get real products.

## The skill

This repo is a **skill pack** - plain markdown an AI agent reads to know how to
call the Zedkr API. One skill:

| Skill | What it does |
|-------|--------------|
| [`order.place`](skills/order.place/SKILL.md) | Place an order from a product link: returns the price and a one-time Pharos wallet to pay; the item ships once it's funded |

## Example

> **You:** Buy this — https://amazon.com/dp/B0DGHYDZ89 — ship to San Francisco.
>
> **Agent:** Locked it in - $1,199. Send **1,199 USDC on Pharos** to
> `0xA1b2…f9` (expires in 30 min).
>
> *(you send the USDC)*
>
> **Agent:** Payment received ✅ - order **ORD-1234567890** placed. It's on the way.

## What you get

- **Real Amazon & eBay products** delivered to 200+ countries
- **Pay in USDC on Pharos** - that's the only thing you touch
- **No account, no card, no KYC** - just an address and an amount
- **Free Prime shipping** on eligible items

## Using the skill pack

Point your agent at this repo and set the API base (in
[`manifest.json`](manifest.json)) to the Zedkr API URL. The agent reads the
`SKILL.md` and calls the endpoints - that's all it needs. See
[`AGENT.md`](AGENT.md) for the order flow.
