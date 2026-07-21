---
name: lead-distro-funnels
description: Build a lead generation funnel (multi-step quiz, appointment booking, or video sales letter page) in Lead Distro AI. Use when the user says "build me a funnel", "make a landing page", "quiz funnel", "VSL page", "booking page", or invokes /funnels. Checks which funnel templates the account has; falls back to an embeddable form when multi-step templates are not yet available.
---

# Lead Distro AI Funnels

Build a hosted lead generation funnel by chat, using the same funnel tools as
`lead-distro-forms`. A funnel is a multi-page flow (quiz steps, trust content,
then a contact form) published to a live link; leads flow into the linked
campaign's normal pipeline.

## Check availability first

Call `list_funnel_templates` before promising anything.

- **If only the simple lead form template is listed**, multi-step funnels are
  not enabled for this account yet. Say so plainly: "Full multi-step funnels
  are coming soon to your account. I can build you an embeddable lead form now,
  which you can drop into an existing page or funnel." Then follow the
  `lead-distro-forms` skill.
- **If quiz / appointment / VSL templates are listed**, build with them using
  the flow below.

## Build flow (when funnel templates are available)

Personalize first, exactly as in `lead-distro-forms`: vertical/niche, goal,
audience + language, offer. Then:

1. Pick the template that matches the goal: **quiz** to qualify before asking
   for contact info, **appointment** to book a call, **VSL** to sell with video
   before the form.
2. Link a campaign + supplier (`list_campaigns`, `list_suppliers`); match every
   form field to the campaign's field mapping (snake_case; `add_campaign_fields`
   for anything missing).
3. `create_funnel`, share the preview link, iterate with `update_funnel`,
   `publish_funnel` only on a clear yes. Deliver the live link (funnels are
   usually run as standalone pages, but the embed code exists too).

## Funnel best practices

- **Qualify before you ask.** Quiz steps come BEFORE the contact form; each
  step asks ONE question with tappable answers. 2 to 4 quiz steps is the sweet
  spot.
- **One goal per funnel.** Every page pushes toward the same single conversion.
- **Earn trust before the form:** trust badges, testimonials, or a short video
  between the quiz and the contact step.
- **Contact form stays short** (4 to 6 fields) and always carries the consent
  line (TCPA-style). Confirmation page tells the visitor exactly what happens
  next ("We'll call you within 15 minutes").
- 5th-grade reading level, benefit-first headline, specific button text,
  mobile-first. Spanish audiences get fully Spanish copy (`language: es`).

## Rules

Same as `lead-distro-forms`: confirm before writes and before publishing, never
invent IDs, "buyers" not "clients", "Lead Distro AI" never "Lead Distro", no em
dashes in visitor-facing copy. If the tools return feature-disabled, Funnels is
a Labs feature: point the user to support@leaddistro.ai and stop.
