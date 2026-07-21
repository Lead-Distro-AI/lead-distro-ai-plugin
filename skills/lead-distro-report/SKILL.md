---
name: lead-distro-report
description: Pull a performance report from Lead Distro AI — revenue, cost, profit/margin, lead counts and acceptance, broken down by campaign, buyer, supplier, state, or status over a date range. Use when the user asks "show me revenue", "how did last week go", "what's my profit on X campaign", "break leads down by buyer", or wants any numbers report. Read-only; never writes. Requires the Lead Distro AI MCP connected.
---

# Pull a Lead Distro AI report

A read-only reporting pass that answers a money or volume question with real
numbers. This skill never calls a write tool.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill).
- The `read` permission is enough.

## Step 1 — Pin the question

Settle three things (ask only what you can't infer):
- **Window** — e.g. last 7 days, last 30, this month, a specific range.
- **Scope** — the whole account, one campaign, one buyer, or one supplier.
- **Cut** — the dimension to break down by (campaign, buyer, supplier, state,
  status), if any.

## Step 2 — Pull the numbers

- Account or filtered totals: `mcp__leaddistro__get_lead_stats` (counts, revenue,
  cost over the window + filters).
- One campaign's P&L: `mcp__leaddistro__get_campaign_performance`.
- A breakdown by a dimension: `mcp__leaddistro__get_lead_breakdown` (group by
  status, buyer, supplier, state…).
- Resolve any names → ids first with `list_campaigns` / `list_buyers` /
  `list_suppliers`.

## Step 3 — Present it

Bottom line first, then the table:
- Lead with the headline number the user asked for (e.g. "Last 7 days: $4,210
  revenue, $2,980 cost, $1,230 profit, 44% margin").
- Then the breakdown as a compact table, sorted by the metric that matters
  (usually profit or volume).
- Count a **Returned** (`DISPUTED`) lead as revenue lost but cost kept — call it
  out if it materially dents margin.
- Keep the language plain; spell out any rate ("acceptance 62%" not just "62").

## Hard rules

- Read-only. Every number comes from a tool call — never estimate.
- Say "buyers" / "suppliers", never "clients" / "vendors-as-buyers".
- If the user then wants to act on a finding, hand off to `lead-distro-audit`
  (diagnose) or the relevant write skill.
- See `reference/concepts.md` for status meanings (especially Returned).
