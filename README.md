# Lead Distro AI plugin for coding agents

Run your [Lead Distro AI](https://www.leaddistro.ai/) account by chatting. This plugin turns your coding agent into a Lead Distro AI expert: set up campaigns, edit field mappings, onboard buyers and suppliers, configure ping-post and the Buyer Portal, build automations, pull reports, and audit account health, all in plain English. It connects to the Lead Distro AI MCP server, so the agent acts on your real account, scoped to your organization.

Lead Distro AI is a lead distribution and pay-per-call platform. This plugin is for operators who want to run it by chat instead of clicking through the dashboard.

Works with **Claude Code** and **Cursor** today. **OpenAI Codex** support is coming.

## What you need

- A Lead Distro AI account with the AI Assistant (MCP) connector enabled (Settings shows an "API Keys" page when it is on). If you do not see it, ask your account owner to enable it from the Labs page.
- Node.js 18+ and a coding agent that supports plugins and MCP.

## Install

One command installs into every coding agent you have:

```bash
npx plugins add Lead-Distro-AI/lead-distro-ai-plugin
```

Restart your agent so the bundled connector loads. The installer keeps the plugin's skills up to date.

## Connect to your account

Installing the plugin registers the Lead Distro AI MCP server automatically. The first time the agent uses a Lead Distro AI tool, you will be asked to sign in and authorize access in your browser. That is it, no key to copy.

Prefer an API key (for a script, or a client that cannot sign in through a browser)? Create one in Lead Distro AI under **Settings > API Keys** (admin only, shown once), then add the connector manually:

```bash
claude mcp add --transport http leaddistro \
  https://mcp.leaddistro.ai/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

Full setup guide: https://www.leaddistro.ai/docs/ai-assistant-plugin

## What you can do

Once connected, just ask. The plugin includes guided recipes and slash commands for the most common jobs:

| Skill | Slash command | Use it to |
|---|---|---|
| **lead-distro-ai** | `/connect` | Connect, and route you to the right recipe. Good first step. |
| **lead-distro-setup-campaign** | `/setup-campaign` | Stand up a new campaign end to end: type and routing, field mapping, inbound filters, and your first buyer and supplier. |
| **lead-distro-onboard-buyer** | `/onboard-buyer` | Add or reconfigure a buyer: price, priority, caps, delivery, and targeting filters. |
| **lead-distro-onboard-supplier** | `/onboard-supplier` | Add a supplier / lead source: cost mode, attach to a campaign, share the inbound API spec. |
| **lead-distro-ping-post** | — | Turn on real-time ping-post bidding and configure which buyers bid before a lead is posted. |
| **lead-distro-buyer-portal** | — | Give a buyer a login to the Buyer Portal and invite their team. |
| **lead-distro-automations** | — | Build automations: notify, deliver to a Sheet or webhook, redistribute or resell leads. |
| **lead-distro-report** | `/report` | Pull revenue, cost, profit, and lead breakdowns over any window. |
| **lead-distro-audit** | `/audit` | Read-only health check with a prioritized fix list. |
| **lead-distro-forms** | `/forms` | Build and publish an embeddable lead capture form wired to a campaign. Get a live link plus an embed code for any page or funnel. |
| **lead-distro-funnels** | `/funnels` | Build a lead generation funnel (quiz, appointment, or VSL page). Falls back to an embeddable form if funnels are not enabled on your account yet. |

### Example prompts

- "Set up a new pay-per-call campaign for auto accident leads in California."
- "Add a buyer to my MVA campaign at $45 per lead, capped at 20 a day, California and Texas only."
- "Turn on ping post for my Solar-CA campaign and let my top three buyers bid."
- "Audit my account. What is set up, what is missing, and what is underperforming this week?"
- "Show me revenue and profit by buyer for the last 7 days."
- "Build me a lead form for my solar campaign that I can embed in my GoHighLevel funnel."

## How it works and what is safe

- Every action is scoped to the organization behind your login or API key. The plugin cannot see or touch another organization's data.
- Write actions (creating a campaign, adding a buyer) run only after the agent summarizes the change and you confirm it.
- The connector is read and setup only. It never moves money: there are no billing, charge, or payout tools.

## Support

- Docs: https://www.leaddistro.ai/docs/ai-assistant-plugin
- Email: support@leaddistro.ai

## License

Apache License 2.0. See [LICENSE](LICENSE).
