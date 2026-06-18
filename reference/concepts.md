# Lead Distro AI — domain reference

Shared concepts for every skill in this suite. Read this when unsure how a field,
status, or routing choice behaves. Keep the vocabulary below exact.

## The unit and the routing model (three columns)

Every campaign is described by three independent choices:

| Choice | Values | Answers |
|---|---|---|
| **Type** | `LEADS`, `CALLS` | What is the unit being sold? |
| **Routing method** | `DIRECT_POST`, `PING_POST` (leads); `STATIC`, `RTB` (calls) | How are buyers matched? |
| **Distribution method** | `WATERFALL`, `ROUND_ROBIN`, `WEIGHTED` | In what order are buyers tried? |

Legal combinations: `LEADS` → `DIRECT_POST` or `PING_POST`. `CALLS` → `STATIC` or
`RTB`. (Calls = pay-per-call. Only use a CALLS campaign if the user explicitly
sells calls.)

**User-facing labels** (say these, not the raw value): `DIRECT_POST` → "Direct
Post", `PING_POST` → "Ping Post", `STATIC` → "Ring Tree", `RTB` → "RTB".

## Distribution methods

- **Waterfall** — buyers are tried in priority order (lower `priority` number =
  tried first). The first buyer who accepts and has cap available wins.
- **Round robin** — eligible buyers take turns evenly. No priority/weight needed.
- **Weighted** — buyers share volume in proportion to their `weight`.

## Ping vs deliver (two independent layers)

- **Routing method** (campaign level) is the *supplier intake* protocol — how
  leads arrive.
- A buyer's **ping** toggle (per buyer on the campaign) is the *buyer dispatch*
  protocol — whether that buyer is pinged for a bid/accept before the lead is
  posted to them. These are independent: a Direct Post campaign can still ping an
  individual buyer before posting.

## Lead statuses

| Value | Shows as | Meaning |
|---|---|---|
| `NEW` | New | Just ingested |
| `VALIDATING` | Validating | Phone validation running |
| `DUPLICATE` | Duplicate | Matched a duplicate rule |
| `DISTRIBUTING` | Distributing | Being routed right now |
| `SENT` | Sent | Delivered, no decision yet |
| `ACCEPTED` | Accepted | Buyer accepted — billable |
| `REJECTED` | Rejected | Buyer rejected at delivery |
| `ERROR` | Error | Delivery attempt failed |
| `DISPUTED` | **Returned** | Returned after acceptance. **Cost is kept — a real loss.** |
| `EXPIRED` | Expired | Aged out before delivery |
| `QUEUED` | Queued | Held by a queue automation; releases later |

Always render `DISPUTED` as "Returned", never "Disputed". When reporting, treat a
Returned lead as a cost the org still paid.

## Field types

Canonical types: String, Number, Boolean, Phone, Email, Date, State, Zip / Postal
Code, URL, List of Allowed Values. Infer smart defaults from a field's name
(phone → Phone, email → Email, zip → Zip / Postal Code). The campaign's
`field_mapping` is the source of truth — never invent field names; read them with
`get_campaign` first.

## Terminology (never drift)

- The entities that buy leads are **buyers**, never "clients".
- The product is **Lead Distro AI** (full name), never "Lead Distro".
- The buyer-facing login is the **Buyer Portal**.
- Lead sources are **suppliers**.

## Safety

Every tool is scoped to one organization. There are **no money-movement tools**
(no billing, charging, or payouts). Write tools act immediately — always summarize
the exact change and get a yes before calling one.
