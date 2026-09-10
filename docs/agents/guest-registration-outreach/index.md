# DRAFT — Duckie Agent: "Guest Registration Outreach"

**Status:** ✅ CREATED 2026-09-09 (status `draft`). Canonical objects:
- Agent: `[DRAFT] Guest Registration Outreach` — `c109c3d5-51e6-4227-9983-a75f1d912066`
  — https://app.useduckie.ai/build/agents?id=c109c3d5-51e6-4227-9983-a75f1d912066
- Runbook: `Age Verification Outreach` — `f9765ef6-3d7a-4b69-90eb-7e69ab9d635a`
- Two Claude sessions built this in parallel; the duplicate set was merged in and removed
  (dup agent `7852663c` deleted; dup runbook `e9dceba5` marked `[SUPERSEDED]`, detached —
  hard-delete in the Duckie UI).
- Agent set to `active` 2026-09-09 (needed for the deployment agent picker; still inert).
- **D1 + D2 built 2026-09-09** (Testing + Paused, internal-notes-only + no-write-actions):
  - Send (D1) `0e009ffe-c8fc-4646-8e2a-913939fdad9f` — Zendesk, New ticket + Tag changed,
    tag any-of `age_verification_outreach`, skips sent/pass_to_gx/ai-nope tags, 60s delay.
  - Reply (D2) `05ebfe54-f29d-4655-9b05-b212a5dd74d0` — Zendesk, Comment added, status
    open+pending, tag any-of `duckie_age_outreach_pending`, skips done/pass_to_gx,
    customer messages only, 60s delay.
- **Sweep BUILT** (board 22/22): runbook `a3f85c6f-0824-4acd-90da-4df802ee2b33`, agent
  `2a2cf8b2-b162-4eac-a088-8d825f81b6b9` (`GX Age Verification — No-Response Sweep`), deployment
  D3 `f41107a6-ad07-42db-abc0-819c8fccc7f6` (Scheduler, hourly, Testing + Paused).
- Auditor group wired = Zendesk `21008954571028`. Guideline trim done (cut `c606cc76`).
- **Still to do:** unpause D1/D2/D3 → test (sweep via Playground — no deploy-level safeguards) →
  review Analyze→Runs → flip to Live + drop D1/D2 safeguards. Sign-offs: follow-up copy;
  Auditor pass-tag/resolution-rule question. Confirm property-ID field `39034953935892` populated.
Companions: `guest-registration-runbook-DRAFT.md` (task #1), `guest-registration-property-config-TABLE.md` (the scaling layer).

One reusable agent for **proactive, property-triggered outreach where we need the guest to
confirm or provide something before the stay** — the "auditor tasks" / pre-arrival-requirements
family. **Task #1 = Age Verification** (25+ HOA rule at Island Retreat + Charming Way, Airbnb only).

Modeled on **GX Support Agent - All Tickets** (`27d9356a-...`) + **[DRAFT] GX Support Agent**
(`6a8f6727-...`). All IDs below are real.

---

## 1. Context (Slack #gx-duckie-internal + the SOP)

- **Task:** new Airbnb reservations at **Island Retreat** units (Port A) + **Charming Way** (30A)
  must confirm the primary guest is **25+** (strict HOA rule). Auditors do this manually today.
  SOP: `docs.google.com/document/d/1R7oUv-z6ZiKo9SXvJ9lHXswxJWyRllikcnkHYF1cjzg`
- **Airbnb only** — IDV covers age on every other channel. (Confirmed via Tet → Desiree.)
- **Trigger already LIVE:** Zendesk triggers `53195816811668` / `53195833875476` stamp the tag
  **`age_verification_outreach`** + an internal note on qualifying Airbnb reservations. Duckie
  fires on the tag.
- **Known data bug:** ticket ↔ reservation ↔ property link unreliable (Francisco eng ticket open,
  backfilling; a Whidbey Island false-positive was seen). The runbook re-verifies the property.
- **Owners:** Sheila Mae Awa (SOP/Auditors), Desiree Saloma (executing), Cameron (PM), Francisco
  Serna (Zendesk triggers), Lina Nguyen (runbooks). Auditor interim assignee: Berlinda.

## 2. Agreed behavior (requirements thread)

| Point | Decision |
|---|---|
| Timing | Immediately on booking confirmation |
| Channel | Airbnb message thread (public reply) |
| After first message | Keep ticket **under Duckie**, set **pending**. No human assignment (Duckie must catch the reply). Auditors monitor a **Zendesk view**. |
| Guest confirms 25+ | Acknowledge, document in an internal note, resolve |
| Guest under 25 | Document, escalate to Auditor / FO queue. No exceptions discussed. |
| No response | Wait **5 days** → hand to Auditors (sweep) |
| Write-back | **Internal note only** for MVP (no custom field / object write). Revisit if Auditors need reporting. |

## 3. Pilot properties

| Property | Voyage UUID | Notes |
|---|---|---|
| Charming Way ("Charming 30A") | `285be117-c579-11f0-b9b2-f3be9c85d368` | Single home, Emerald Coast – 30A-BG |
| Island Retreat #110 ("Seas The Day") | `62ef1a60-fbb3-11f0-8857-5d39d9268bf8` | Port Aransas |
| Island Retreat #122 ("Beach Please!") | `39f0c3de-5ab7-44e9-9f21-0a69be826316` | Port Aransas |

The runbook's property re-check accepts these three UUIDs (plus name/market match as a fallback).
Expand the allow-list as more Island Retreat units are enrolled.

---

## 4. Proposed core-object payload (`object_type: "agents"`)

| Field | Value |
|---|---|
| `name` | `[DRAFT] Guest Registration Outreach` |
| `description` | `Proactive property-triggered pre-arrival outreach. Task #1 = Age Verification (Island Retreat + Charming Way, 25+ HOA rule, Airbnb only): fires on age_verification_outreach, sends the SOP message on the Airbnb thread, parks pending under Duckie, then records a 25+ confirmation and resolves or escalates under-25 / non-response to Auditors. Not a general support agent.` |
| `status` | `draft` |
| `start_with_type` / `autonomous_runtime_version` | `autonomous` / `v1` |
| `model_config` | `{ "model": "gpt-5.5", "reasoning": { "effort": "medium" } }` |
| `resolution_tracking` | `true` |
| `resolution_rule_ids` | `["3da8de17-5bd3-401a-9d20-0d9f484372c7", "7bdccde1-6ee9-4793-ae3c-d70f1c465f90", "79352b60-c42e-42d6-b379-f0838abf60f5", "2dab77a1-0520-4f4f-a587-b6e789552617"]` |
| `knowledge_tags` / `attribute_ids` / `category_ids` / `callable_agent_ids` | `[]` |
| `folder_id` | `null` |
| `included_runbook_ids` | `["<new: Age Verification Outreach>"]` |

### `tool_ids`

```
duckie-knowledge-search
duckie-support-knowledge-tree
duckie-read-support-knowledge
duckie-query-agent-table          # scaling layer (Pre-Arrival Requirements table)
duckie-save-value
duckie-sleep
duckie-create-alert
9cfc31f4-8474-4770-905d-b00d43218fdf   # User API (booking + property + guest, by contact)
3cb18e2a-147b-408a-a38a-79aebadd7253   # getBookingByCode
d7775191-d82f-451d-a4be-334aa3f3be81   # findBookingByIdentifier
f8a2cec9-a76a-4c3e-9340-72930e7bd27a   # Property API (by property UUID) — property re-verification
app-zendesk-get-ticket
app-zendesk-get-ticket-comments
app-zendesk-get-user
app-zendesk-search
app-zendesk-create-ticket-reply
app-zendesk-create-ticket-note
app-zendesk-add-tags
app-zendesk-remove-tags
app-zendesk-set-ticket-status
app-zendesk-set-ticket-custom-status
app-zendesk-assign-ticket
app-zendesk-assign-ticket-to-group
```
No SMS/email tool for MVP (Airbnb-only). `app-zendesk-update-ticket-custom-fields` omitted —
add it if/when write-back to a field is wanted.

### `guideline_ids` / `guardrail_ids`

Same real-ID subsets carried from earlier drafts. Most load-bearing here: guideline `7c74b863`
(Never Confirm Actions You Can't Verify), `18f136d4` guardrail (Airbnb content restrictions — no
links/phone/email), `6a8ec787` (Use Escalation/Resolution runbooks), `1d531ac2` (handover
incomplete if Duckie still assignee), `be782466`/`01ee33d7` ([Failed Delivery]). Full lists in the
git history of this file / the earlier draft.

---

## 5. Instructions (thin)

**You are AvantStay's Guest Registration Outreach agent (GX).** You run only on proactive
pre-arrival tickets carrying a task tag you're configured for. Today that is
**`age_verification_outreach`** → follow the **Age Verification Outreach** runbook exactly.

**Skip rules** (end silently): human-assigned · already handled (`duckie_age_outreach_sent` and no
`duckie_age_outreach_pending`) · property re-check ≠ an enrolled property (fire `duckie-create-alert`,
end) · myaskai / "AI Agent (Inquiries)" content is noise.

Never promise or imply an HOA age exception. Never assign the ticket to Duckie AI Agent on a handover.

---

## 6. Deployments (Duckie UI)

- **D1 send:** Zendesk `ticket.tag_changed` (+ `ticket.created` backstop) · includeAny
  `age_verification_outreach` · exclude `duckie_age_outreach_sent`, `duckie_pass_to_gx`,
  `ai_not_allowed_to_answer`, `ai_agent_should_not_answer` · author any · status new/open ·
  delay ~60s · **mode: testing**
- **D2 reply:** Zendesk `ticket.comment_added` · includeAny `duckie_age_outreach_pending` ·
  exclude `duckie_age_outreach_done`, `duckie_pass_to_gx` · author `customer` · status open/pending
  · delay ~60s · **mode: testing**
- **Sweep (5-day):** cron hourly, mirror **Stale Pending Sweep** (`bd4e3204-...`) — find
  `duckie_age_outreach_pending`, last Duckie message 5+ days old, no guest reply → hand to Auditors.

---

## 7. Scaling past task #1 — recommendation for the "hundreds of these"

The worry: every new requirement means Francisco builds another Zendesk trigger + tag, someone
writes another runbook, another deployment. Hundreds of those doesn't scale.

**It doesn't have to be hundreds of anything except table rows.** The structure:

| Layer | How many, ever | Who maintains |
|---|---|---|
| **Requirement *types*** (age min, vehicle/parking registration, HOA form, quiet-hours ack, occupancy re-confirm, municipal STR registration, pool waiver, …) — each = one runbook section + one message template | ~10–25 total, grows slowly | Lina (runbooks) |
| **Property → which requirements apply** (+ any per-property detail, + channel/cohort conditions like "Airbnb only") | Hundreds of rows — but it's **data**, bulk-loadable from a spreadsheet/CSV | Ops (Desiree / Auditors) |
| **Trigger** | **One** generic Zendesk trigger / Duckie deployment on new reservations — Duckie does the per-property requirement lookup | Francisco (once) |
| **Agent** | **One** — this one | — |

So: a new *property* joining an existing requirement = one row (seconds, no eng). A new
*requirement type* = one runbook section + template (an afternoon, rare). Eng involvement after
the initial setup ≈ zero.

**Migration path (don't build all of it now):**
1. **Now:** ship task #1 on the existing `age_verification_outreach` tag to prove the mechanics.
2. **Next:** stand up the `Pre-Arrival Requirements` table (see the table doc) + a single generic
   trigger. Move age verification into it; retire the bespoke trigger.
3. **Then:** each subsequent requirement is table rows + a runbook section. Bulk-load the backlog
   from a spreadsheet once the pattern is proven.

**Do not** attempt to enumerate and build all the requirements up front. Pilot → generalize → bulk-load.

---

## 8. BLOCKER — not created yet

Session's Duckie connector exposes only read tools. Need `duckie_create_core_object` /
`duckie_update_core_object` (`api:core:write`). Reconnect the connector + fresh session → create
runbook → create agent. **Or** build it by hand in the Duckie UI from §4–6 (Agent Tables are
UI-only regardless). A UI build checklist can be produced on request.

---

## 9. Open items (for Francisco / Desiree)

1. **Trigger direction** (for Francisco/Justin) — see the question drafted in the chat: can one
   generic trigger on new reservations (or the "Reservation custom object created" event from the
   prior HOA-outreach doc) replace per-requirement triggers, with Duckie doing the requirement
   lookup?
2. **Auditor Zendesk group ID** for under-25 / 5-day handoffs (Desiree / Sheila).
3. **Property linkage** — is custom field `39034953935892` trustworthy yet, or hard-stop + alert
   on mismatch until the fix lands? (Francisco's eng ticket.)
4. **Outbound SMS** — not needed for this Airbnb-only MVP. When a future requirement needs SMS
   (Booking.com / Expedia / direct), scope the Zendesk/Twilio outbound path with Francisco
   (likely a custom tool; the "Select Outbound SMS #" ticket field suggests a mechanism exists).

## 10. Decisions locked

Agent `[DRAFT] Guest Registration Outreach` · Task #1 Age Verification · Trigger
`age_verification_outreach` · Airbnb thread only · Capture mode · SOP message verbatim · 25+ =
ack + note + resolve · under-25 / 5-day = escalate to Auditors · write-back = internal note only ·
per-property table = the scaling layer (phase 2) · model `gpt-5.5`/medium · `draft` / `testing` ·
copy **accepted, proceed to test**.
