---
name: lead-distro-onboard-buyer
description: Guided onboarding of a buyer onto a Lead Distro AI campaign — price, priority/weight, daily/weekly/monthly caps, state and field filters, and attaching them to one or more campaigns. Use when the user wants to "add a buyer", "onboard a buyer", "set up a new client to receive leads", or change a buyer's caps/price/targeting in Lead Distro AI. Requires the Lead Distro AI MCP connected.
---

# Onboard a buyer in Lead Distro AI

Add or reconfigure a buyer and attach them to a campaign with the right price, caps,
and targeting. Confirm before every write.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill to connect).
- Key needs `campaigns:write` for the write steps.

## Step 1 — Find the campaign and any existing buyer

- `mcp__leaddistro__list_campaigns` → resolve the target campaign id.
- `mcp__leaddistro__list_buyers` → check whether this buyer already exists (reuse it,
  don't create a duplicate).
- `mcp__leaddistro__get_campaign` on the target → see the current buyers, the
  distribution method (so you know whether to set `priority` or `weight`), and the
  field mapping (so filters reference real fields).

## Step 2 — Create the buyer (if new)

`mcp__leaddistro__create_buyer` with `name` and optional `email`, `phone`, `location`.
If the buyer already exists, skip to Step 3 and use their id.

## Step 3 — Attach to the campaign

Confirm the terms, then `mcp__leaddistro__add_buyer_to_campaign`:
- `price` — what the buyer pays per lead.
- Ordering — `priority` for WATERFALL (lower = tried first) or `weight` for WEIGHTED.
  For ROUND_ROBIN neither is needed.
- Caps — `dailyCap` / `weeklyCap` / `monthlyCap` / `totalCap` as given (omit for
  unlimited).
- `filters` — state/zip/field targeting so the buyer only gets matching leads.

To change an existing attachment instead, use `mcp__leaddistro__update_campaign_buyer`.

## Step 4 — Targeting and quality rules

Translate the user's intent into `filters`:
- "Only Texas and California" → a state filter on the campaign's state field.
- "Only at-fault accidents" → a field filter on `at_fault`.
- Use the exact field names from the campaign's field mapping (Step 1), never guesses.

## Step 5 — Confirm and recap

Show the final buyer config (campaign, price, ordering, caps, filters) and confirm.
After writing, recap the buyer id and what they will now receive. If the user also
needs delivery setup (API post, email, Google Sheets) or suppression lists, note that
those are configured on the buyer in-app (the MCP covers routing terms, not every
delivery integration yet).

## Hard rules

- Say "buyer", never "client".
- Never set caps or price the user didn't confirm.
- One buyer can be on several campaigns — confirm which campaign each change targets.
