---
name: lead-distro-onboard-supplier
description: Guided onboarding of a supplier (lead source / vendor) onto a Lead Distro AI campaign — create the supplier, set its cost mode (fixed cost per lead, revenue share, or none), attach it to one or more campaigns, and hand over the inbound API spec. Use when the user wants to "add a supplier", "onboard a vendor", "connect a new lead source", or change a supplier's cost in Lead Distro AI. Requires the Lead Distro AI MCP connected.
---

# Onboard a supplier in Lead Distro AI

Add a lead source and attach it to a campaign with the right cost terms, then give
the supplier what they need to start sending leads. Confirm before every write.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill to connect).
- Key needs `campaigns:write` for the write steps.

## Step 1 — Find the campaign and any existing supplier

- `mcp__leaddistro__list_campaigns` → resolve the target campaign id.
- `mcp__leaddistro__list_suppliers` → reuse an existing supplier, don't duplicate.
- `mcp__leaddistro__get_campaign` → see the field mapping and routing method (a
  `PING_POST` campaign expects the supplier to ping first, then post).

## Step 2 — Create the supplier (if new)

`mcp__leaddistro__create_supplier` with `name` and optional contact fields. If the
supplier already exists, use its id.

## Step 3 — Attach to the campaign and set cost

- `mcp__leaddistro__add_supplier_to_campaign` to attach.
- `mcp__leaddistro__set_supplier_cost_mode` to set how this supplier costs you:
  - **Fixed** — a set cost per accepted lead (you pay the supplier per lead).
  - **Revenue share** — the supplier earns a percentage of the buyer revenue.
  - **None** — your own traffic, no per-lead supplier cost (cost comes from ad
    spend instead).
  Confirm the exact number/percent before writing.
- `mcp__leaddistro__update_campaign_supplier` to change an existing attachment.

## Step 4 — Hand over the inbound spec

`mcp__leaddistro__get_campaign_api_spec` → give the supplier the endpoint, the
required fields (from the field mapping), and, for ping-post, the ping vs post
flow. Offer to run `mcp__leaddistro__send_test_lead` to confirm routing works
before they go live.

## Step 5 — Confirm and recap

Recap supplier id, the campaign, the cost mode + number, and the next step
(send a test lead, or share the API spec). 

## Hard rules

- Suppliers are **lead sources**; buyers **buy**. Don't mix them up.
- Never set a cost the user didn't confirm.
- Read the field mapping before quoting required fields — never guess field names.
- See `reference/concepts.md` for cost-mode and routing details.
