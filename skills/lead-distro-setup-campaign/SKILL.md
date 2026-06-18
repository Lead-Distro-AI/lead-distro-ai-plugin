---
name: lead-distro-setup-campaign
description: Guided setup of a new Lead Distro AI campaign end to end — campaign type and routing, field mapping, inbound filters, and the first buyer and supplier. Use when the user wants to "set up", "create", or "launch" a campaign in Lead Distro AI, or says they have a new lead source / new buyer to stand up. Requires the Lead Distro AI MCP connected (see the lead-distro-ai router skill).
---

# Set up a Lead Distro AI campaign

Stand up a working campaign through the MCP, one confirmed step at a time. Never
fire a write tool without first showing the user exactly what it will do.

## Preconditions

- The `mcp__leaddistro__*` tools must be loaded. If not, send the user to the
  `lead-distro-ai` router skill to connect first.
- The connected key needs the `campaigns:write` permission for the write steps.

## Step 0 — Understand the goal

Ask (or infer from context) the few things that shape everything else:
- **Vertical / what the leads are** (legal MVA, solar, insurance, home services…).
- **Unit:** leads or calls. (Calls = pay-per-call; only if the user says so.)
- **How leads arrive:** their own ads/form (one supplier) vs. multiple vendors.
- **Who buys:** one buyer or several, and how they should split (priority/waterfall,
  round robin, or weighted).

Don't over-interview. Two or three answers is enough to start; fill the rest with
sensible defaults and confirm.

## Step 1 — Check what exists

Call `mcp__leaddistro__list_campaigns`, `mcp__leaddistro__list_buyers`, and
`mcp__leaddistro__list_suppliers` so you reuse existing buyers/suppliers instead of
creating duplicates, and so the new campaign name isn't a collision.

## Step 2 — Create the campaign

Summarize the plan, get a yes, then `mcp__leaddistro__create_campaign` with:
- `name`, `vertical`
- `campaignType` (`LEADS` unless the user said calls)
- `routingMethod` (`DIRECT_POST` for a single posting supplier; `PING_POST` for an
  auction across buyers — only if the user wants real-time bidding)
- `distributionMethod` (`WATERFALL` / `ROUND_ROBIN` / `WEIGHTED`)
- `status` (`PAUSED` while you finish wiring it; flip to `ACTIVE` at the end)
- `dailyCap` if they gave one

## Step 3 — Field mapping

Call `mcp__leaddistro__update_field_mapping` with the fields a lead should carry.
- Always include the basics: `first_name`, `last_name`, `phone` (Phone),
  `email` (Email).
- Add vertical fields (e.g. legal: `incident_date` Date, `case_type` List of
  Allowed Values, `at_fault` Boolean; home services: `service_type`, `zip` Zip /
  Postal Code).
- Mark the fields that must be present as `required: true`.
Confirm the field list with the user before writing — this defines what every lead
must look like.

## Step 4 — Inbound filters (optional)

If the user wants to reject some leads up front (wrong state, missing consent, out
of range), call `mcp__leaddistro__update_inbound_filters` with `{ field, operator,
value }` rules. Skip if they don't need it.

## Step 5 — Add a supplier

Reuse an existing supplier or `mcp__leaddistro__create_supplier`, then
`mcp__leaddistro__add_supplier_to_campaign` with `cost_per_lead` and any
`daily_cap`. The supplier is the lead source; its API key is how leads get posted in.

## Step 6 — Add the first buyer

Reuse or `mcp__leaddistro__create_buyer`, then `mcp__leaddistro__add_buyer_to_campaign`
with `price`, `priority`/`weight` (per the distribution method), and caps
(`dailyCap`/`weeklyCap`/`monthlyCap`). For state/field targeting, pass `filters`.
For deeper buyer config (delivery method, suppression), hand off to
`lead-distro-onboard-buyer`.

## Step 7 — Go live

Show a final summary (campaign, fields, supplier, buyer, caps). On the user's
confirmation, `mcp__leaddistro__update_campaign` to set `status: ACTIVE`. Then call
`mcp__leaddistro__get_onboarding_status` and report anything still missing.

## Output

End with a short recap: campaign name + id, the supplier and buyer wired up, the
required fields, and the ingest next step (the supplier posts leads to the ingest
API with their key — point them to the campaign's API Specs page in-app).
