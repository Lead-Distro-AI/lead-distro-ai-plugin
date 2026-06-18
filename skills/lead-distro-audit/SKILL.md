---
name: lead-distro-audit
description: Read-only health check of a Lead Distro AI account — what's set up vs missing, which campaigns are underperforming, cap fulfillment, and buyer/supplier coverage. Use when the user asks "how's my account", "audit my campaigns", "what's wrong", "what should I fix", or "is anything underperforming" in Lead Distro AI. Read-only; never writes. Requires the Lead Distro AI MCP connected.
---

# Audit a Lead Distro AI account

A fast, read-only pass over the account that surfaces gaps and underperformance and
ends with a short, prioritized fix list. This skill never calls a write tool.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill).
- The `read` permission is enough.

## Step 1 — Onboarding gaps

`mcp__leaddistro__get_onboarding_status` → list anything not set up (a campaign with
no buyers, no supplier, no field mapping, etc.). These are the highest-priority fixes
because they block leads from flowing.

## Step 2 — Inventory

`mcp__leaddistro__list_campaigns`, `mcp__leaddistro__list_buyers`,
`mcp__leaddistro__list_suppliers`. Note:
- Campaigns that are `PAUSED` but otherwise complete (likely should be live).
- Buyers/suppliers not attached to any campaign (dead weight or forgotten setup).

## Step 3 — Performance

For each active campaign, `mcp__leaddistro__get_campaign_performance` over a sensible
window (last 7 and 30 days). Flag:
- Negative or thin margin (cost close to or above revenue).
- Low acceptance rate (many leads not accepted by buyers).
- Caps never filling (under-delivery) or always maxing (leaving demand on the table).
Use `mcp__leaddistro__get_lead_breakdown` to localize a problem (e.g. one buyer
rejecting most leads, one state with poor acceptance).

## Step 4 — Report

Write a short report, bottom line first:
- **Fix now** — onboarding gaps and anything blocking leads.
- **Worth attention** — margin/acceptance/cap issues, ranked by dollar impact.
- **Looks healthy** — a one-line confirmation of what's working.

For each finding, name the campaign/buyer and the specific number behind it (don't
just say "low acceptance" — say "acceptance 38% on Solar-CA, vs 71% account-wide").
Keep the language plain. Offer to fix the top items via the
`lead-distro-setup-campaign` or `lead-distro-onboard-buyer` skills (which do write).

## Hard rules

- Read-only. If a fix is needed, recommend it and hand off; do not write here.
- Every claim cites a number you actually pulled from a tool. No guessing.
- Say "buyers", never "clients".
