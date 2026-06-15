# Lead Distro AI plugin for Claude

Run your [Lead Distro AI](https://www.leaddistro.ai/) account by chatting. This plugin lets Claude set up campaigns, edit field mappings, onboard buyers and suppliers, configure automations, and audit account health, all in plain English. It connects to the Lead Distro AI MCP server, so Claude acts on your real account, scoped to your organization.

Lead Distro AI is a lead distribution and pay-per-call platform. This plugin is for operators who want to run it by chat instead of clicking through the dashboard.

## What you need

- A Lead Distro AI account with the AI Assistant (MCP) connector enabled (Settings has an "API Keys" page when it is on). If you do not see it, ask your account owner to enable it from the Labs page.
- Claude Code, or another Claude client that supports plugins and MCP.

## Install

```bash
claude plugin marketplace add Rafael805/lead-distro-ai-plugin
claude plugin install lead-distro-ai@lead-distro-ai-plugin
```

Or, inside a Claude Code session:

```
/plugin marketplace add Rafael805/lead-distro-ai-plugin
/plugin install lead-distro-ai@lead-distro-ai-plugin
```

## Connect to your account

Installing the plugin registers the Lead Distro AI MCP server automatically. The first time Claude uses a Lead Distro AI tool, you will be asked to sign in and authorize access in your browser. That is it.

Prefer an API key (for a script, or a client that cannot sign in through a browser)? Create one in Lead Distro AI under **Settings > API Keys** (admin only, shown once), then add the connector manually:

```bash
claude mcp add --transport http leaddistro \
  https://mcp.leaddistro.ai/mcp \
  --header "Authorization: Bearer YOUR_KEY"
```

Full setup guide: https://www.leaddistro.ai/docs/ai-assistant-mcp

## What you can do

Once connected, just ask. The plugin includes guided recipes for the most common jobs:

| Skill | Use it to |
|---|---|
| **lead-distro-ai** | Connect, and route you to the right recipe. Good first step. |
| **lead-distro-setup-campaign** | Stand up a new campaign end to end: type and routing, field mapping, inbound filters, and your first buyer and supplier. |
| **lead-distro-onboard-buyer** | Add or reconfigure a buyer: price, priority, caps, delivery, and targeting filters. |
| **lead-distro-audit** | Read only health check: what is set up versus missing, which campaigns are underperforming, cap fulfillment, and buyer and supplier coverage. |

### Example prompts

- "Set up a new pay-per-call campaign for auto accident leads in California."
- "Add a buyer to my MVA campaign at $45 per lead, capped at 20 a day, California and Texas only."
- "Audit my account. What is set up, what is missing, and what is underperforming this week?"
- "List my campaigns and show me last week's revenue and cost."

## How it works and what is safe

- Every action is scoped to the organization behind your login or API key. The plugin cannot see or touch another organization's data.
- Write actions (creating a campaign, adding a buyer) run only after Claude summarizes the change and you confirm it.
- The connector is read and setup only. It never moves money: there are no billing, charge, or payout tools.

## Support

- Docs: https://www.leaddistro.ai/docs/ai-assistant-mcp
- Email: support@leaddistro.ai

## License

Apache License 2.0. See [LICENSE](LICENSE).
