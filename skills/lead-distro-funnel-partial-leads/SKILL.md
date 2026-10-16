---
name: lead-distro-funnel-partial-leads
description: "Use when the user wants to keep people who typed a phone number or email and then quit before finishing: \"capture partial leads\", \"save abandoned leads\", \"send me the people who drop off after the phone step\". Not finding why visitors quit (see funnel-cro). Requires the Lead Distro AI MCP connected."
---

# Save visitors who leave early

The steps for this job live on the Lead Distro AI server, so they always match its current tools.

1. Call `mcp__leaddistro__get_skill` with `slug: "funnel-partial-leads"`.
2. Follow the procedure it returns, step by step.
3. If the result has a `permission_note`, tell the user before you start: this connection cannot call some of the tools the job needs.

For any other job, call `mcp__leaddistro__list_skills` and pick the matching skill. Every skill is also listed in Lead Distro AI under Settings > MCP & API > Skills.

## If the Lead Distro AI tools are missing

Installing this plugin registers the Lead Distro AI MCP server (it ships an `.mcp.json`). The first time a `mcp__leaddistro__*` tool runs, the client opens a browser to sign in and authorize. No API key needed.

If the tools are not present, restart the client so the bundled connector loads (install: `npx plugins add Lead-Distro-AI/lead-distro-ai-plugin`), then call any read tool such as `list_campaigns` and finish the browser sign in. Full guide: https://www.leaddistro.ai/docs/ai-assistant-plugin
