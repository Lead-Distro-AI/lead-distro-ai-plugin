---
name: lead-distro-automations
description: Build an automation on a Lead Distro AI campaign — trigger an action (notify via Slack/email, deliver to a Sheet or webhook, redistribute or resell a lead) when something happens to a lead. Use when the user wants to "automate", "notify me when", "send rejected leads to", "resell aged leads", or "redistribute unsold leads" in Lead Distro AI. Requires the Lead Distro AI MCP connected.
---

# Build an automation in Lead Distro AI

Create a rule that runs an action when a lead event fires. Confirm before writing.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill).
- Key needs `campaigns:write`.

## Step 1 — Read the campaign

`mcp__leaddistro__get_campaign` → confirm the campaign, its field mapping (actions
reference real fields), and existing automations (don't duplicate one).

## Step 2 — Pin the trigger and action

Settle two things with the user:
- **Trigger** — the lead event (e.g. lead rejected, lead unsold, lead aged, new
  accepted lead).
- **Action** — what to do: notify (Slack/email), deliver (webhook / Google Sheet),
  or redistribute/resell the lead to another campaign or buyer.

## Step 3 — Create it

`mcp__leaddistro__create_automation` with the trigger + action + any target
(campaign, buyer, Sheet, webhook URL, recipients). Summarize the full rule in one
sentence ("When a lead is rejected on Solar-CA, post it to the Aggregator
campaign") and get a yes before writing.

## Step 4 — Recap

Confirm the automation id and restate, in plain English, exactly when it fires and
what it does. Note that redistribution preserves the lead's locked cost.

## Hard rules

- Confirm the trigger + action together before writing — automations run on real
  leads immediately.
- Use exact field names from the mapping, never guesses.
- Say "buyers", never "clients". See `reference/concepts.md` for lead statuses.
