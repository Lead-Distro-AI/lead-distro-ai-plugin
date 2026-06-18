---
name: lead-distro-buyer-portal
description: Set up the Lead Distro AI Buyer Portal for a buyer — enable the portal, configure its settings, and invite a buyer's team members so they can log in to see their leads. Use when the user wants to "give a buyer a login", "enable the buyer portal", "invite a buyer to the portal", or "let my buyer see their leads" in Lead Distro AI. Requires the Lead Distro AI MCP connected.
---

# Set up the Buyer Portal in Lead Distro AI

Give a buyer a login so they can see the leads they bought. Confirm before every
write.

## Preconditions

- `mcp__leaddistro__*` tools loaded (see the `lead-distro-ai` router skill).
- Key needs `campaigns:write`.

## Step 1 — Find the buyer

`mcp__leaddistro__list_buyers` → resolve the buyer id. `mcp__leaddistro__get_portal_members`
→ see who already has access (don't re-invite someone who's already in).

## Step 2 — Enable the portal

`mcp__leaddistro__enable_buyer_portal` for that buyer if it isn't on yet.

## Step 3 — Configure settings

`mcp__leaddistro__configure_portal_settings` → set what the buyer sees and can do
(e.g. which fields are visible, whether they can return/dispute leads). Confirm the
settings with the user before writing.

## Step 4 — Invite members

`mcp__leaddistro__add_portal_member` for each person on the buyer's team, with their
email. Recap who was invited and what they'll be able to see.

## Step 5 — Recap

Confirm the portal is on, list the members invited, and note that the buyer logs in
at the Buyer Portal. 

## Hard rules

- It is the **Buyer Portal**, never the "client portal" in anything the user reads.
- Don't invite a member the user didn't name. Confirm emails before sending invites.
- Say "buyers", never "clients".
