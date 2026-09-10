# DRAFT — Runbook: "Age Verification Outreach"

**Status:** ✅ CREATED 2026-09-09 as `Age Verification Outreach` —
`f9765ef6-3d7a-4b69-90eb-7e69ab9d635a`, `runbook_type` agent, `status` draft. Attached to agent
`c109c3d5-51e6-4227-9983-a75f1d912066`. Live content differs slightly from this file (tool
@mentions wired, "primary guest 25+" copy, interim Auditor routing via `duckie_age_verification_failed`
+ GX until the real Auditor group ID lands, `avantstay_backend_error` alerting) — see Duckie.
A duplicate build (`e9dceba5`) from a parallel session was merged in and marked `[SUPERSEDED]`.

Task #1 for the `[DRAFT] Guest Registration Outreach` agent. `runbook_type`: agent. `status`: draft.
Source of truth for the process: [SOP — Island Retreat & Charming Way Guest Age Verification](https://docs.google.com/document/d/1R7oUv-z6ZiKo9SXvJ9lHXswxJWyRllikcnkHYF1cjzg).

**Scope:** new **Airbnb** reservations at **Island Retreat** units (Port Aransas) and **Charming
Way** (Emerald Coast – 30A-BG). The Zendesk trigger has already filtered to these — the ticket
arrives tagged `age_verification_outreach`. This runbook re-verifies, sends the SOP message,
parks the ticket, and handles the guest's reply.

---

## Step 1 — Which run is this?

- Ticket has `duckie_age_outreach_pending` **and** the trigger is a customer comment → **Step 6
  (process reply)**.
- Otherwise → first contact, continue.

## Step 2 — Eligibility (end the run silently unless noted)

1. `duckie_age_outreach_sent` present and no `duckie_age_outreach_pending` → already handled, end.
2. Ticket assigned to a human agent → end.
3. Not an Airbnb booking (no `airbnb_messaging` / booking channel ≠ Airbnb) → end. *(The trigger
   should prevent this; belt and suspenders.)*
4. myaskai / "AI Agent (Inquiries)" comments → treat as noise, ignore.

## Step 3 — Re-verify the property (data-integrity guard)

The ticket ↔ reservation ↔ property link has been unreliable (Francisco's open eng ticket).

1. Read property_id from ticket custom field `39034953935892`; also read Booking Code (Hash)
   `21563820224916` → **getBookingByCode** (fallback: requester email/phone → **User API**).
2. Confirm the resolved property is on the enrolled allow-list (UUID match; property-name +
   market match as fallback):
   - `285be117-c579-11f0-b9b2-f3be9c85d368` — Charming Way ("Charming 30A"), Emerald Coast 30A
   - `62ef1a60-fbb3-11f0-8857-5d39d9268bf8` — Island Retreat #110 ("Seas The Day"), Port Aransas
   - `39f0c3de-5ab7-44e9-9f21-0a69be826316` — Island Retreat #122 ("Beach Please!"), Port Aransas

   Full Island Retreat set (for when more units are enrolled):
   `voyage.avantstay.com/properties/finance?markets=f46e201d-4b57-430b-b688-1ed687d45ef2&name=island%20retreat`
3. **Mismatch** (tag present but property is not one of these — e.g. the Whidbey Island
   false-positive): do **not** message the guest. Fire `duckie-create-alert`
   (`age_verification_outreach` misfire, ticket id, resolved property), post an internal note,
   add `duckie_age_outreach_sent` so it isn't retried, end.
4. Hold: `guest_first_name`, `property_short_name`, booking channel.

## Step 4 — Send the SOP message (public reply on the Airbnb thread)

Substitute `{{guest_first_name}}`. No links, phone numbers, or email addresses (Airbnb rules,
guardrail `18f136d4`). Use the SOP text **verbatim**:

```
Hi {{guest_first_name}},

Thank you for booking with us!

Before your upcoming stay, we just need to confirm one requirement for the property. Due to strict
HOA regulations, the primary guest must be 25 years of age or older.

Could you please confirm that you are at least 25 years old?

Thank you, and we look forward to hosting you!

Best,
AvantStay
```

## Step 5 — Park the ticket

1. **Verify delivery.** If a `[Failed Delivery]` / bounce note appears, do not re-send; follow
   the `[Failed Delivery]` guidelines (roll back, silent handover to the Auditor queue, keep open).
2. **Add Tags** (one call): `duckie_age_outreach_sent, duckie_age_outreach_pending, duckie_gx_processed, duckie_replied`.
3. Internal note: *"Age verification outreach sent (25+ HOA requirement). Awaiting guest reply.
   5-day follow-up window. Auditors monitor via view."*
4. Set status **pending**. Keep the ticket under Duckie (do **not** assign to a human).
5. End the run. The reply comes back on D2; non-response is caught by the sweep.

## Step 6 — Process the guest's reply

Re-verify you're still on `duckie_age_outreach_pending`. Read the latest guest comment(s) and
classify:

### 6a — Guest confirms 25+ (clear yes: "yes", "I'm 34", "we're all over 25", etc.)
1. Internal note: *"Guest confirmed primary guest is 25+. Age verification complete."* — quote the
   guest's words. **MVP: internal note only, no custom-field write.**
2. Public reply: *"Thank you for confirming, {{guest_first_name}} — you're all set. We look
   forward to hosting you!"*
3. Add Tags: `duckie_age_outreach_done`; remove `duckie_age_outreach_pending`.
4. **Resolution Process.**

### 6b — Guest says under 25, or that the primary guest is under 25
1. Do **not** discuss or imply an exception (SOP + guardrails).
2. Internal note: *"Guest indicated primary guest is under 25. HOA 25+ requirement unmet —
   escalating to Auditors / FO for review per SOP."* Quote the guest's exact words.
3. Public reply (brief, neutral): *"Thanks for letting us know, {{guest_first_name}}. Our team
   will follow up with you shortly about this requirement."*
4. Per the Escalation Process: clear the Duckie assignee, assign the **Auditor queue**
   (`<GROUP ID — TBD>`), add `duckie_age_outreach_done`, `duckie_pass_to_reservations` (or the
   auditor-specific pass tag), `duckie_silent_passover` is **not** used here (a reply was sent),
   remove `duckie_resolved` if present, keep status **open**.

### 6c — Ambiguous / partial / unrelated reply
- Ask once, targeted: *"Just to confirm — is the primary guest on this reservation at least 25
  years old?"* Keep `duckie_age_outreach_pending`. Max **two** total asks, then escalate to the
  Auditor queue per 6b's routing with an internal note.
- If the guest raises a different issue (a real question, change, complaint) → answer nothing,
  internal note, hand to `[MAIN] GX Support` / Auditors per the Escalation Process.

## Step 7 — Sweep: no-response → follow-up, then Auditor handoff (separate scheduled agent/deployment)

**UPDATED 2026-09-09 — now a two-stage sweep (decision: send a follow-up first).** The live
runbook (`f9765ef6`) Step 7 is the source of truth. Summary:
- **Stage A** — `duckie_age_outreach_pending`, no `duckie_age_outreach_followup_sent`, outreach
  5+ days old OR check-in <24h: send ONE follow-up message on the Airbnb thread (verbatim copy in
  the live runbook), tag `duckie_age_outreach_followup_sent`, verify delivery, keep pending.
- **Stage B** — also has `duckie_age_outreach_followup_sent`, follow-up 2+ days old OR check-in
  passed/<2h: hand to Auditors per Step 6b routing, remove `duckie_age_outreach_pending`, add
  `duckie_age_outreach_done`, status open. No further guest message.
Still not built (needs its own agent + runbook + Scheduler deployment). Follow-up copy wants
Sheila/Desiree sign-off.

---

## Review status — ACCEPTED, proceeding to test

Copy and behavior locked (live runbook `f9765ef6` is source of truth):
- SOP message used **verbatim**; **primary guest 25+** wording.
- 25+ → one-line thank-you + note + resolve.
- Under-25 → brief neutral holding reply, then handoff to Auditors.
- Follow-up → **UPDATED**: one follow-up message at day 5 (or 24h pre-check-in), then Auditors.

**Auditor routing wired 2026-09-09:** Auditor handoffs (Step 6b / Step 7 Stage B) assign to the
**Auditors** Zendesk group `21008954571028` (interim assignee Berlinda) + `duckie_age_verification_failed`
tag + internal note, status open. Still open: whether Auditors want their own pass tag +
resolution rule (ask Desiree/Sheila); follow-up copy sign-off.
