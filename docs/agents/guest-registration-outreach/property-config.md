# DRAFT — Agent Table: "Guest Registration — Property Config"

> **PHASE 2 — not needed for the Age Verification MVP.** For task #1 the message is identical
> across every enrolled property (the SOP defines one message for all Island Retreat units +
> Charming Way), and property/channel enrollment is handled by Francisco's Zendesk trigger, not
> by Duckie. This table earns its place when **either** a second proactive task ships **or** a
> property needs bespoke copy.
>
> See **§7 "Scaling past task #1"** in `guest-registration-agent-DRAFT.md` for the recommended
> end-state: ~10–25 requirement *types* (runbook sections), hundreds of property→requirement rows
> (this table, bulk-loadable), **one** generic trigger, **one** agent. This doc is that table's
> schema. When built for real it's better named **`Pre-Arrival Requirements`** with the `task`
> column renamed `requirement`.

The per-property / per-task config layer for the `[DRAFT] Guest Registration Outreach` agent.
**Built in Duckie UI → Build → Tables** (Agent Tables are not writable via the MCP connector).
Read at run time with the `duckie-query-agent-table` tool — the same mechanism the
**"Notify Slack Team"** workflow already uses for its region→Slack table (`4fc22351-...`).

---

## Proactive-outreach task inventory (from Slack — the "we have lots of these")

The `age_verification_outreach` task is the first of a family. Others already discussed:

| Task | Trigger / cohort | Status | Owner |
|---|---|---|---|
| **Age verification 25+** | Airbnb bookings at Island Retreat (Port A) + Charming Way (30A) — HOA rule | Tag `age_verification_outreach` **live**; Duckie side = this build | Desiree / Sheila |
| Vehicle / parking registration | Reservations at gated communities needing a permit before arrival | Scoped Jul 21 (`#duckie-avantstay`), not built | Cameron / Duckie |
| Other HOA confirmations | Per-community requirements ("beginning of being able to expand this to other HOA processes" — Francisco) | Concept | Francisco / Desiree |
| Orphan-night outreach | Single-night gaps between bookings — proactively offer to fill | Discussed (Ashrit), not built | Cameron / Igo |
| Natural-disaster / area-event comms | Custom-field "event" tied to impacted properties → SOP-driven messaging | Blocked – eng (Aug 24 sync) | Ashrit / Lina |
| International pre-arrival outreach | International guests arriving in next 90 days | Chef report cohort exists | Lina |

When ≥2 of these run through the `Guest Registration Outreach` agent, this table becomes the
place that maps **property × task → message + capture config**, so a new task is a set of rows
plus a runbook section rather than a new agent.

---

## Why a table (not Zendesk tags)

- The agent already knows the property — ticket custom field **`39034953935892`** carries the
  property UUID (this is how the Notify Slack Team workflow gets it). No tag needed for identity.
- Tags can't hold "always mention the gate code is texted separately; don't mention the hot tub —
  it's out of service; nearest grocery is 20 min away." A table row can.
- One row per property scales; hundreds of per-property tags do not.
- Maps 1:1 onto Voyage property fields later, if/when eng moves this to the system of record.

---

## Table definition

- **Name:** `Guest Registration — Property Config`
- **Row scope:** `org` (shared registry — every run of the agent reads the same rows)
- **row_key:** `{property_id}:{task}` — stable, lets ops replace a row cleanly
- **Description (for the agent):** *"One row per property that is enrolled in Guest Registration
  outreach. Look up the row for the current property_id and task=guest_registration. If no row or
  enabled=false, do not send anything. Otherwise follow mode, channel_mode, and the message fields."*

### Columns

| Column | Type | Required | Purpose |
|---|---|---|---|
| `property_id` | text | yes | Voyage property UUID (from ticket custom field `39034953935892`). Part of row_key. |
| `property_name` | text | yes | Human reference, e.g. `Seas The Bay`. |
| `market` | text | no | e.g. `port_aransas`. |
| `task` | text | yes | `guest_registration` (the dormant hook — always this value for now). Part of row_key. |
| `enabled` | boolean | yes | Agent sends only if `true`. Pilot = only pilot properties `true`. |
| `mode` | text | yes | `inform` (one-shot, point to self-serve) or `capture` (ask → await reply → record). |
| `channel_mode` | text | no | `auto` (default: OTA-thread / SMS / email+SMS by booking channel) or an override: `sms_only`, `email_only`, `email_and_sms`, `thread_only`. |
| `message_instructions` | text | yes | Free-form, property-specific: what this property's registration message MUST say and MUST NOT say. The agent composes = base template + this. Guardrails always outrank it. |
| `template_override` | text | no | A full verbatim message that replaces the base template entirely (still `{placeholder}`-substituted, still channel-adapted). Use when a property wants exact copy. |
| `capture_prompt` | text | no | `capture` mode only: the exact question to ask the guest (e.g. *"Please reply with the full name and age of every guest in your party."*). |
| `capture_fields` | json | no | `capture` mode only: the structured fields expected back, e.g. `["guest_names","guest_ages","hoa_ack"]` — used to validate completeness and to write back. |
| `writeback_target` | text | no | `capture` mode only: `internal_note` (default) / `custom_field:<id>` / `endpoint:<name>`. |
| `reminder_hours` | number | no | `capture` mode only: hours of guest silence before the sweep sends one reminder (default 24). |
| `owner` | text | no | Who maintains this row (GX/ops accountability). |
| `notes` | text | no | Internal change log / context. |

---

## How the agent uses it (runbook step)

```
1. Read property_id from ticket custom field 39034953935892 (fallback: booking lookup).
2. duckie-query-agent-table:
     table: Guest Registration — Property Config
     filters: [
       {"column":"property_id","op":"eq","value":"{{property_id}}"},
       {"column":"task","op":"eq","value":"guest_registration"},
       {"column":"enabled","op":"eq","value":true}
     ]
     limit: 1
3. No row  -> end run silently, internal note "property not enrolled in Guest Registration".
4. Row    -> carry mode, channel_mode, message_instructions, template_override,
              capture_prompt, capture_fields, writeback_target, reminder_hours into the runbook.
```

---

## Pilot seeding (do in the UI)

Start with 2–5 rows, `enabled = true`, everyone else absent. Example row:

| field | value |
|---|---|
| `property_id` | `bcd8df6e-9b73-4383-b1ec-58541ac2fe75` *(Seas The Bay — confirm the real UUID)* |
| `property_name` | `Seas The Bay` |
| `market` | `port_aransas` |
| `task` | `guest_registration` |
| `enabled` | `true` |
| `mode` | `capture` *(Port A age confirmation)* or `inform` |
| `channel_mode` | `auto` |
| `message_instructions` | *"Port Aransas requires every reservation to confirm all guests are 25+. Ask the guest to confirm the number of guests and that all are 25 or older. Mention parking is 2 vehicles max, permits in the entryway drawer. Do not mention the community pool — it is seasonal and currently closed."* |
| `capture_prompt` | *"To finish your registration for Seas The Bay, please reply with: (1) total number of guests, and (2) confirmation that everyone in your party is 25 or older."* |
| `capture_fields` | `["guest_count","all_guests_25_plus"]` |
| `writeback_target` | `internal_note` |
| `reminder_hours` | `24` |
| `owner` | `Desiree` |

---

## Open items

1. **Property UUID source of truth** — confirm ticket custom field `39034953935892` is reliably
   populated on `stay_booked` tickets (the Notify Slack Team workflow depends on it, so likely yes).
2. **`writeback_target` for capture mode** — Francisco's earlier call was to lean on Zendesk
   custom objects/fields over a new backend endpoint. Decide the concrete field(s).
3. **Who owns the table** — recommend GX/ops (Desiree / Lina) maintain rows; PM signs off on new
   `message_instructions`.
