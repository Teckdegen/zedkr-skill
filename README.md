# Zedkr

**We buy Amazon & eBay products for you - pay with USDC on Pharos.**

Zedkr is a set of skills that let an AI agent shop Amazon and eBay on your behalf.
You say what you want, the agent finds it, and you pay with USDC on Pharos. The
product ships to your door. No accounts, no card, no checkout pages - just an
address to send USDC to.

## How it works

```
1. Search        "Find me an iPhone 16 Pro Max" → the agent shows you the options
2. Pick          You choose the exact product you want from that list
3. Place order   Give a shipping address - Zedkr locks in that product's price and
                 hands back a Pharos wallet address + the exact USDC amount.
4. Pay           Send that USDC on Pharos to the address.
5. Done          Once it's received, Zedkr buys that item and it ships to you.
```

The agent never picks for you - search returns options, **you choose one**, and
that exact product is what gets ordered. Pay in USDC, get real products.

## The skills

This repo is a **skill pack** - plain markdown files an AI agent reads to know how
to call the Zedkr API. Two skills:

| Skill | What it does |
|-------|--------------|
| [`product.search`](skills/product.search/SKILL.md) | Search Amazon or eBay and get product info - title, price, rating, link |
| [`order.place`](skills/order.place/SKILL.md) | Place an order: returns the price and a one-time Pharos wallet to pay; the item ships once it's funded |

## Example

> **You:** Find me an iPhone 16 Pro Max, ship to San Francisco.
>
> **Agent:** Here are a few - 256GB $1,199 ⭐4.8 · 512GB $1,399 ⭐4.7 · … Which one?
>
> **You:** The 256GB.
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
[`manifest.json`](manifest.json)) to the Zedkr API URL. The agent reads each
`SKILL.md` and calls the endpoints - that's all it needs. See
[`AGENT.md`](AGENT.md) for the full search → order flow.
