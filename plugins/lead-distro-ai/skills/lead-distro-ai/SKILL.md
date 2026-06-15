---
name: lead-distro-ai
description: Router and shared setup for the Lead Distro AI skill suite. Use when the user asks about Lead Distro AI at a general level ("connect Lead Distro", "set up a campaign in Lead Distro", "Lead Distro status") to decide which sub-skill to invoke, or to look up how to connect the MCP and which tools exist. Also use the first time a user wants to drive their Lead Distro AI account by chat.
---

# Lead Distro AI Suite

Router and shared conventions for managing a Lead Distro AI account through chat.
Lead Distro AI is a multi-tenant lead distribution and pay-per-call platform. This
suite drives its **remote MCP server** so operators can set up campaigns, edit
field mappings, onboard buyers and suppliers, configure automations, and audit
account health in plain English.

## Connecting (do this once)

The skills call tools exposed by the Lead Distro AI MCP server. The user connects
their AI client to it with an org-scoped API key:

1. In Lead Distro AI: **Settings → API Keys → Create key** (admin only). Copy the
   key once (it is shown only once).
2. Add the connector. For Claude Code:
   ```bash
   claude mcp add --transport http leaddistro \
     https://mcp.leaddistro.ai/mcp \
     --header "Authorization: Bearer YOUR_KEY"
   ```
   For Cursor / Windsurf, add to the client's MCP config:
   ```json
   { "mcpServers": { "leaddistro": { "url": "https://mcp.leaddistro.ai/mcp",
     "headers": { "Authorization": "Bearer YOUR_KEY" } } } }
   ```
3. Restart / reconnect the client so the `mcp__leaddistro__*` tools load.

If the `mcp__leaddistro__*` tools are not present, the user has not connected yet —
walk them through the steps above before doing anything else.

Full setup guide: `https://www.leaddistro.ai/docs/ai-assistant-mcp`.

## Which sub-skill

| The user wants to… | Invoke |
|---|---|
| Stand up a new campaign (type, fields, buyers, filters) | `lead-distro-setup-campaign` |
| Add and configure a buyer (caps, price, delivery, filters) | `lead-distro-onboard-buyer` |
| Check account health / what's missing / what's underperforming | `lead-distro-audit` |
| A one-off read or single edit | call the MCP tool directly (see below) |

## MCP tools used

All tools are org-scoped (they act on the org behind the API key) and gated by the
key's permissions (`read`, `campaigns:write`). Money movement is never exposed.

### Read & reporting (`read`)
- `mcp__leaddistro__list_campaigns` — all campaigns (id, name, status, type, caps)
- `mcp__leaddistro__get_campaign` — one campaign + its buyers, suppliers, field mapping, filters
- `mcp__leaddistro__list_buyers` / `mcp__leaddistro__list_suppliers`
- `mcp__leaddistro__get_lead_stats` — counts, revenue, cost by date range + filters
- `mcp__leaddistro__get_lead_breakdown` — leads grouped by a dimension (status, buyer, supplier, state…)
- `mcp__leaddistro__get_campaign_performance` — P&L + lead stats for a campaign
- `mcp__leaddistro__get_onboarding_status` — what's set up vs missing

### Campaign & field setup (`campaigns:write`)
- `mcp__leaddistro__create_campaign` / `mcp__leaddistro__update_campaign`
- `mcp__leaddistro__update_field_mapping` — define the fields leads carry
- `mcp__leaddistro__update_inbound_filters` — accept/reject rules before distribution
- `mcp__leaddistro__create_buyer` / `mcp__leaddistro__update_buyer`
- `mcp__leaddistro__add_buyer_to_campaign` / `mcp__leaddistro__update_campaign_buyer`
- `mcp__leaddistro__create_supplier` / `mcp__leaddistro__update_supplier` / `mcp__leaddistro__add_supplier_to_campaign`
- `mcp__leaddistro__create_automation`

## Hard rules

- **Confirm before any write.** Unlike the in-app assistant, MCP write tools run
  immediately. Summarize exactly what you will create or change and get a yes
  before calling a `*write*` tool.
- **Read before you write.** Call `get_campaign` / `list_buyers` first so you edit
  the right IDs and don't duplicate existing buyers or fields.
- **Never invent IDs.** Resolve campaign / buyer / supplier IDs from a list tool.
- **Terminology:** say "buyers", never "clients". The product is "Lead Distro AI".
- **Field types:** use Lead Distro's canonical types (String, Number, Boolean,
  Phone, Email, Date, State, Zip / Postal Code, URL, List of Allowed Values). Infer
  smart defaults from field names (phone → Phone, email → Email, zip → Zip / Postal Code).
- If a tool returns `{ success: false, error }`, surface the error plainly and stop;
  do not retry a write blindly.
