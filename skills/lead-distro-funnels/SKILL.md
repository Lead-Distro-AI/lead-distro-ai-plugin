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

- **The funnel owns the headline.** Unlike an embeddable form (which must have
  NO headline), a funnel is a standalone page: the first step opens with a
  `hero` block carrying an eyebrow ("Free and confidential. No obligation."), a
  benefit-first headline, and a short subheadline ("Answer a few quick
  questions... takes less than 2 minutes"). Trust badges under the first step's
  answers.
- **Emoji icons on answer buttons (default).** Every quiz answer option takes
  both an `emoji` and a matching `icon`, e.g.
  `{ value: "car", label: "Car accident", emoji: "🚗", icon: "car" }`. The
  funnel's `theme.optionIconStyle` ("emoji" by default, or "icon" / "none")
  picks which one visitors see, so filling in both is what lets the operator
  switch the whole form's look later without rewriting copy.
  - **Never put an emoji in the `label`.** A label is plain words only
    ("Car accident", not "🚗 Car accident") or the emoji renders twice.
  - Pick an emoji that literally depicts the answer, and keep the style
    consistent within a step. All options on a step get a mark, or none do.
  - If nothing fits an answer, leave that whole step's options bare rather
    than reaching for a decorative emoji. Same when the user opts out or the
    vertical makes emojis feel off (use judgment).
- **One question per step (default).** Each qualifying question gets its OWN
  step page, one `quiz_step` block per page, with big tappable answers. Never
  stack two qualifying questions on one step. 2 to 4 quiz steps is the sweet
  spot. The ONLY step that groups multiple inputs is the contact step (see
  below).
- **Qualify before you ask.** Quiz steps come BEFORE the contact form.
- **Group contact info onto ONE step.** Name, phone, and email go together on a
  single contact `form` block at the end (this is the one place multiple inputs
  share a step). Default that step to: one **`full_name`** field (NOT separate
  `first_name` / `last_name`), `phone` (required), and `email` (**required**).
- **A Back button appears automatically** on every step after the first, so you
  never add one yourself. Just order the steps sensibly; the renderer handles
  back navigation, and the browser's own Back button also returns to the
  previous step (prior answers stay filled in).
- **One goal per funnel.** Every page pushes toward the same single conversion.
- **Earn trust before the form:** trust badges, testimonials, or a short video
  between the quiz and the contact step.
- **Contact form stays short** (3 to 5 fields) and always carries the consent
  line (TCPA-style). Confirmation page tells the visitor exactly what happens
  next ("We'll call you within 15 minutes").
- 5th-grade reading level, benefit-first headline, specific button text,
  mobile-first. Spanish audiences get fully Spanish copy (`language: es`).

## Look and feel (our blocks by default, flexibility on request)

Default to Lead Distro AI's own blocks and the best practices above. **Never ask
the user to choose between "custom code" and "our blocks"**: that is your call,
and the ladder below settles it. Climb it in order and never skip a rung.

1. **Settings first.** Almost every look is a theme key or a block field:
   `theme.logoUrl` (upload their file with `upload_funnel_asset`, never invent a
   URL), `theme.primaryColor` (the button color), `accentColor`,
   `backgroundColor`, `textColor`, `fontFamily`, `radius`; the progress bar's
   `progressFillColor`, `progressTrackColor`, `progressThickness`,
   `progressLabelStyle` ("Step 3 of 6" or "Question 3 of 6") and
   `progressMarker` (an emoji or uploaded image riding the fill edge, a car
   driving along the bar); quiz option `emoji` / `icon` / `imageUrl`; the
   `footer` block's disclaimers, links and copyright. **Button text picks a
   readable color on its own** (white on a dark fill, near-black on yellow or
   lime), so use the brand's real button color even when it is light;
   `theme.buttonTextColor` overrides it if the brand insists.
2. **Then Custom CSS on top of our blocks** (`definition.customCss`), only for a
   look no setting offers: a shadow or hover lift on the answer cards, a
   gradient progress fill, letter-spacing. Target the documented hooks, which
   are identical on the live page, the builder canvas and the HTML export:
   `.ld-progress`, `.ld-progress-label`, `.ld-progress-pct`,
   `.ld-progress-track`, `.ld-progress-fill`, `.ld-progress-marker`, `.ld-logo`,
   `.ld-back`, `.ld-quiz-opt`, `.ld-cta`, `.ld-headline`, `.ld-field`,
   `.ld-footer`, and `.ld-n-<blockId>` for any single block. The `create_funnel`
   tool description carries the current list and is the source of truth. Never
   use CSS for layout or positioning, never write `body` / `html` rules, never
   `!important` a block's own class. The operator can see and edit this CSS
   under Settings > Advanced.
3. **Last, a `custom_code` block** (`{ type: 'custom_code', html }`) for what
   blocks and CSS cannot express, with `upload_funnel_asset` for any SVG or JPG
   it needs. Its scripts run on the published page, not on the builder canvas.

**When the user shows a reference** (a screenshot or another funnel's URL):
match their **brand** faithfully with rung 1: logo, `primaryColor` set to their
real button color, `progressFillColor` the same, their label wording, and their
footer small print, links and copyright word for word. Keep **our step
structure** (question inline on the first step, one question per step, contact
last) unless they explicitly ask for the reference's structure, such as a
button-only first page, a three-column answer grid, or no progress bar. When
they do ask, build exactly that with rungs 1 and 2 and no pushback: it is their
funnel.

**After building:** share the draft URL, name the two or three places it
differs from the reference and why, and ask before `publish_funnel`.

## Compliance footer (default on every funnel)

Every funnel gets a small-print disclaimer footer by default: a `text` block at
the BOTTOM of each step page (at minimum the first step and the contact step),
written in the funnel's language. Build it from three layers:

1. **General funnel compliance (always):**
   - Results/outcome disclaimer: any numbers, dollar amounts, or testimonials
     shown are examples from past results and do not guarantee or predict the
     visitor's outcome; individual results vary.
   - What submitting means: the info is for an initial evaluation only;
     submitting authorizes contact about the request (this complements, never
     replaces, the TCPA consent line next to the submit button).
   - A © line with the business name and current year.
2. **TCPA:** the contact step's consent text stays next to the button and names
   the user's business; the footer must never contradict it.
3. **Industry-specific (pick what fits the vertical):**
   - **Legal / mass tort:** "This is a legal advertisement." The operator is
     not a law firm and does not give legal advice; prior results do not
     guarantee similar outcomes; submitting does not create an attorney-client
     relationship; "no fee unless you win" refers to attorney fees, costs may
     still apply. California traffic: joint advertising language naming the
     sponsoring attorney(s) is required.
   - **Insurance / Medicare:** not affiliated with or endorsed by any
     government agency; a licensed agent may call; not all products available
     in all states. Medicare traffic needs the "not connected with the federal
     Medicare program" line.
   - **Finance / solar / home services:** savings figures are estimates and
     depend on the visitor's situation; the operator connects visitors with
     third-party providers and is not the lender/installer/provider itself
     (when that's the model).

Tailor the exact wording to the user's business (ask what they are: a law firm
vs. a marketing/referral service changes the required language) and remind them
you're not their lawyer: they should have counsel review regulated-vertical
copy. Never let the funnel promise a specific dollar outcome anywhere.

## Rules

Same as `lead-distro-forms`: confirm before writes and before publishing, never
invent IDs, "buyers" not "clients", "Lead Distro AI" never "Lead Distro", no em
dashes in visitor-facing copy. If the tools return feature-disabled, Funnels is
a Labs feature: point the user to support@leaddistro.ai and stop.
