---
name: lead-distro-ping-post
description: Turn on and configure real-time ping-post bidding for a Lead Distro AI campaign — set the campaign's ping-post settings, then per buyer decide who gets pinged for a bid before the lead is posted and how each accepted lead is delivered. Use when the user wants to "set up ping post", "let buyers bid in real time", "ping my buyers before sending", or "configure ping post" in Lead Distro AI. Requires the Lead Distro AI MCP connected.
---

# Configure ping-post in Lead Distro AI

Set up real-time bidding so buyers are pinged for a price/accept decision before a
lead is posted to the winner. Confirm before every write.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill).
- Key needs `campaigns:write`.
- Read `reference/concepts.md` first: routing method (campaign) and a buyer's ping
  toggle are independent layers.

## Step 1 — Read the campaign

`mcp__leaddistro__get_campaign` → confirm the campaign type is `LEADS`, see its
routing method, its buyers, and the field mapping. Ping payloads only carry the
fields you mark as ping-required, so know the mapping before configuring.

## Step 2 — Configure ping-post at the campaign

`mcp__leaddistro__configure_ping_post` → set the campaign's ping-post settings
(e.g. enabling the real-time exchange and which fields are sent in the ping vs the
full post). Summarize exactly what will be sent in a ping before writing — a ping
should never include full PII the buyer hasn't paid for.

## Step 3 — Per-buyer ping + delivery

For each buyer who should bid:
- `mcp__leaddistro__set_buyer_ping` → turn that buyer's ping on and set its
  endpoint/terms.
- `mcp__leaddistro__set_buyer_delivery` → set how the won lead is delivered to
  them (API post, email, Google Sheets) at the locked price.

## Step 4 — Test and recap

Offer `mcp__leaddistro__send_test_lead` to run a synthetic lead through the ping →
post flow and confirm a buyer is selected and delivered. Recap which buyers bid,
what the ping sends, and how delivery happens.

## Hard rules

- A ping must not leak fields the buyer hasn't bought — confirm the ping payload.
- Never enable a buyer's ping without a reachable endpoint configured.
- Say "buyers", never "clients".
