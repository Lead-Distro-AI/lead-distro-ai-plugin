---
name: lead-distro-forms
description: Build, edit, and publish an embeddable lead capture form in Lead Distro AI. Use when the user says "build me a lead form", "make a form for my campaign", "embed a form on my page / funnel / GoHighLevel / WordPress", "capture leads on my site", or invokes /forms. Every submission flows into their chosen campaign and is validated, deduped, routed, and billed like any other lead.
---

# Lead Distro AI Forms

Build a hosted, embeddable lead capture form by chat. The form is created and
published with the funnel tools on the Lead Distro AI MCP server; the user gets a
live link plus an embed code to paste into any page builder (GoHighLevel,
WordPress, Webflow, plain HTML). Submissions post into their campaign's normal
ingest pipeline, so validation, duplicate detection, routing, and billing all
work unchanged.

## Before you build: personalize (mandatory)

Never build from a bare "make me a form." First ask, in one short message:

1. **Vertical / niche** — what kind of leads? (solar, auto insurance, legal, home services…)
2. **Goal** — what should a submission lead to? (a call, a quote, an appointment)
3. **Audience** — who fills this out, and in what language? (English/Spanish)
4. **Offer** — what does the visitor get for submitting? (free quote, consultation, guide)

Use the answers to write the field labels, question copy, button text, and
confirmation copy. Skip questions the user already answered.

## Build flow

1. **Discover.** `list_funnel_templates` for available templates,
   `list_campaigns` (and `get_campaign`) to pick which campaign receives the
   leads, and `list_suppliers` for the source the leads are attributed to. If
   the account has no obvious supplier for web forms, offer to create one
   (e.g. "Website Forms") with `create_supplier` and attach it with
   `add_supplier_to_campaign`.
2. **Match fields.** Form field names must be snake_case and must exist in the
   campaign's field mapping (`get_campaign` shows it). If a field is missing,
   add it with `add_campaign_fields` before publishing. Baseline fields:
   `full_name`, `phone`, `email` (default one **`full_name`** field, not
   separate `first_name` / `last_name`; make `email` required by default). Add
   `first_name` / `last_name` only if the campaign's mapping already splits the
   name that way.
3. **Create.** `create_funnel` with the template, the campaign + supplier link,
   and the personalized copy. Show the user the preview link that comes back.
4. **Iterate.** Apply changes with `update_funnel` (full definition each time).
   Keep edits conversational: "make the headline about savings", "add a zip
   code field", "switch it to Spanish".
5. **Publish only on a clear yes.** `publish_funnel` makes it live. Hand back
   BOTH deliverables and explain each in one line:
   - the **live link** (share it directly or run traffic to it), and
   - the **embed code** (paste into any page or funnel; the iframe auto-sizes).

## Copy best practices (bake these in)

- **NO headline, hero, or marketing copy.** A form is an EMBEDDABLE component:
  the page it's pasted into owns the headline, subheadline, eyebrow, trust
  badges, and sales copy. Never add `hero`, `cta`, `testimonials`, or
  `trust_badges` blocks to a form. The form is just the question steps and the
  contact fields. If the user wants a headline and a full page, that's a funnel:
  use the `lead-distro-funnels` skill instead.
- **Emoji icons on answer buttons (default).** Every quiz answer option takes
  both an `emoji` and a matching `icon`, e.g.
  `{ value: "car", label: "Car accident", emoji: "🚗", icon: "car" }`. The
  form's `theme.optionIconStyle` ("emoji" by default, or "icon" / "none") picks
  which one visitors see, so filling in both is what lets the operator switch
  the whole form's look later without rewriting copy.
  - **Never put an emoji in the `label`.** A label is plain words only
    ("Car accident", not "🚗 Car accident") or the emoji renders twice.
  - Pick an emoji that literally depicts the answer, and keep the style
    consistent within a step. All options on a step get a mark, or none do.
  - If nothing fits an answer, leave that whole step's options bare rather
    than reaching for a decorative emoji. Same when the user asks for no emojis
    or the vertical is somber enough that they'd feel off (use judgment, e.g.
    funeral services).
- **One CTA.** One form, one button, one goal per page.
- **One question per step for a multi-step form.** If the form qualifies with
  several questions, give each qualifying question its own step (one per page),
  then group the contact fields (name, phone, email) together on the final step.
  A Back button appears automatically on every step after the first (and the
  browser's own Back button steps back too), so don't build one.
- **Default the name to one `full_name` field**, not separate first/last, and
  make `email` required by default.
- **Short forms convert.** 3 to 5 fields. Every extra field costs submissions.
- **5th-grade reading level.** Short sentences, no jargon, benefit-first
  headline ("Get your free quote in 60 seconds"), specific button text
  ("Get My Quote", never "Submit").
- **Consent line is required.** Every form includes consent text next to the
  button (TCPA-style: consent to be contacted by phone/text). Never remove it;
  tailor it to the user's business name.
- **Mobile-first.** Most traffic is phones; keep copy tight.
- **Spanish audiences:** set the definition language to `es` and write ALL copy
  in Spanish, not a translation bolted onto English structure.

## Rules

- Confirm before any write tool, and especially before `publish_funnel`.
- Never invent campaign, supplier, or funnel IDs; resolve them from list tools.
- Say "buyers", never "clients". The product is "Lead Distro AI", never "Lead
  Distro". No em dashes in copy that lands on the form or in front of visitors.
- If the funnel tools return a feature-disabled or not-found error, Forms is a
  Labs feature that may not be enabled for this organization yet: tell the user
  to contact support@leaddistro.ai to have Forms turned on, and stop.
- Test before handing off: suggest submitting a test entry and checking it
  arrives in the Leads page for the campaign.
