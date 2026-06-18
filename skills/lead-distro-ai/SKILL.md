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

Installing this plugin registers the Lead Distro AI MCP server automatically (it
ships an `.mcp.json`). The **first time** a `mcp__leaddistro__*` tool runs, the
client opens a browser for the user to **sign in and authorize** (OAuth). No API
key needed. After that the connection refreshes itself.

If the `mcp__leaddistro__*` tools are not present:

1. Confirm the plugin installed and the client was restarted so the bundled MCP
   loaded. The cross-agent install is one line:
   ```bash
   npx plugins add Rafael805/lead-distro-ai-plugin
   ```
2. Trigger any read tool (e.g. `list_campaigns`) and complete the browser sign in
   when prompted.

**API key fallback** (scripts, or a client that cannot sign in through a browser):
create a key in Lead Distro AI under **Settings → API Keys** (admin only, shown
once), then register the connector manually:
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

Full setup guide: `https://www.leaddistro.ai/docs/ai-assistant-plugin`.

## Which sub-skill

| The user wants to… | Invoke |
|---|---|
| Stand up a new campaign (type, fields, buyers, filters) | `lead-distro-setup-campaign` |
| Add and configure a buyer (caps, price, delivery, filters) | `lead-distro-onboard-buyer` |
| Add a supplier / lead source (cost mode, attach to a campaign) | `lead-distro-onboard-supplier` |
| Turn on real-time ping-post bidding for a campaign | `lead-distro-ping-post` |
| Give a buyer a login to the buyer portal | `lead-distro-buyer-portal` |
| Build an automation (notify, deliver, redistribute, resell) | `lead-distro-automations` |
| Pull a report: revenue, cost, P&L, lead breakdown | `lead-distro-report` |
| Check account health / what's missing / what's underperforming | `lead-distro-audit` |
| A one-off read or single edit | call the MCP tool directly (see below) |

Slash commands (shortcuts to the recipes above): `/connect`, `/setup-campaign`,
`/onboard-buyer`, `/onboard-supplier`, `/audit`, `/report`.

Shared domain reference (campaign model, lead statuses, routing/distribution,
terminology) lives in `reference/concepts.md` — read it once when unsure how a
field or status behaves.

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
- `mcp__leaddistro__get_lead` / `mcp__leaddistro__get_lead_distribution_trail` — one lead + where it went
- `mcp__leaddistro__get_onboarding_status` — what's set up vs missing
- `mcp__leaddistro__get_campaign_api_spec` — the inbound API spec to give a supplier
- `mcp__leaddistro__get_portal_members` — who has buyer-portal access

### Campaign & field setup (`campaigns:write`)
- `mcp__leaddistro__create_campaign` / `mcp__leaddistro__update_campaign`
- `mcp__leaddistro__update_field_mapping` / `mcp__leaddistro__add_campaign_fields` — define the fields leads carry
- `mcp__leaddistro__update_inbound_filters` — accept/reject rules before distribution
- `mcp__leaddistro__create_buyer` / `mcp__leaddistro__update_buyer`
- `mcp__leaddistro__add_buyer_to_campaign` / `mcp__leaddistro__update_campaign_buyer`
- `mcp__leaddistro__set_buyer_delivery` / `mcp__leaddistro__set_buyer_ping` — per-buyer delivery + ping config
- `mcp__leaddistro__create_supplier` / `mcp__leaddistro__update_supplier` / `mcp__leaddistro__add_supplier_to_campaign`
- `mcp__leaddistro__set_supplier_cost_mode` / `mcp__leaddistro__update_campaign_supplier`
- `mcp__leaddistro__configure_ping_post` — turn on real-time ping-post bidding
- `mcp__leaddistro__create_automation`
- `mcp__leaddistro__enable_buyer_portal` / `mcp__leaddistro__add_portal_member` / `mcp__leaddistro__configure_portal_settings`

### Test (`leads:write`)
- `mcp__leaddistro__send_test_lead` — push a synthetic lead through a campaign to verify routing

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
