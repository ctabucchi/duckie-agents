# Duckie Weekly Sync — August 4, 2026
*Since last sync (Jul 14) · Sources: #proj-duckie-avantstay, #gx-duckie-integration-internal, Duckie runs, Linear, Jul 28 sync notes*

---

## Key metrics

| Metric | Value |
|---|---|
| Resolution rate | 30–37% (Jul 13) → **41.3% record** (Aug 3) |
| Team goal | 60% full resolution — remaining named blocker: tapechart/availability access (ISGI, the other named blocker, has shipped) |

---

## Shipped since last sync

| Item | Shipped | Detail |
|---|---|---|
| Taxonomy V3 / AI-agent-allowed toggle | Jul 15 | Per-category AI toggle (Mateus/Rodrigo). Race-condition violations dropped 7/109 → 1/76 after a fix. |
| Booking status (IDV/Payment/Guest Agreement) | Jul 15 | `getBookingStatus` + `getCheckInBlockers` (Rodrigo) — guest-facing blockers + Booking Hub link instead of raw Voyage status. |
| ISGI case creation | Jul 27 | `createCase` MCP tool (Rodrigo) — Duckie opens real ISGI cases, deduped, tied to ZD ticket. |
| ECI end-to-end + LCO creation | Jul 29–30 | Full ECI (pre-arrival + same-day) + LCO endpoint (Francisco), added to runbooks. |

---

## Status board

Per Ashrit's ask (Aug 4): eng-blocked split out from ops/process-blocked. Folds in Jul 28 sync decisions and today's Desiree updates.

| Status | Item | Detail | Owner |
|---|---|---|---|
| 🟢 Working on | IDV resolution push | 15% → 80% target this week | Desiree / Cameron |
| 🟢 Working on | Lockbox/access code push | 21% → 80% target this week | Desiree / Cameron |
| 🟢 Working on | Reservation alteration push | 13% → 80% target this week | Desiree / Cameron |
| 🟢 Working on | VIP/hotel tagging split | Splits VIP-non-hotel (do-not-answer) from hotel (answerable); target was Aug 1 — **unconfirmed, check today** | Francisco |
| 🟢 Working on | Contact-update "silent handoff" fix | Not yet deployed — Igo's last update (Aug 3, 4:25pm) was "we will release in the next days," no confirmation since. [Thread](https://avantstay.slack.com/archives/C0B1SK218F7/p1785435072589209) · [BODI-3431](https://linear.app/avantstay/issue/BODI-3431/review-guest-contact-update-for-duckie-confirmations). Long-term: move to booking-hash ID | Rodrigo / Igo |
| 🟢 Working on | ECI/LCO VAS creation error | Rodrigo deployed a fix at 7:54am (separate thread, "could not be confirmed, please handoff") — but Desiree hit an ECI/LCO VAS creation error again tonight, re-flagged, **not yet closed out** | Desiree / Rodrigo / Francisco |
| 🟢 Working on | ISGI categorization & dedup QA | Field team (Jolee) flagged a volume jump this morning; reviewing today | Raneem |
| 🟢 Working on | Slack Field Ops escalation — ready to scale | Works well for 1-question/1-answer threads. New: 1hr auto-follow-up → auto hand-back to GX if still no reply (replaces a 6hr/12hr version that wasn't reliable). Desiree: ready for gradual rollout to more markets. [Example](https://avantstay.zendesk.com/agent/tickets/4903375) | Desiree |
| 🔵 Scoped | Availability / tapechart access | Aligned as "next project" Jul 28; one of two named 60%-goal blockers (ISGI, the other, now live) | Igo / Francisco |
| 🔵 Scoped | Proactive outbound guest messaging | Scoped Jul 21; needs reservation trigger, data endpoint, outbound channel — none built | Cameron / Duckie team |
| 🔵 Scoped | Spoke / offshore integration | Paused 2 weeks as of Jul 28 for stability focus — revisit ~Aug 11 | Group |
| 🔴 Blocked – eng ★ | **Stay Context / ZD trigger fix** — top priority | Aug 4 dashboard: 37% no-reply (77/210), worst no-reply gap on the board. Root cause: 3 events land on an Airbnb ticket at once, ZD trigger auto-solves it before anyone replies (~40% of the contact-details gap). **Eng work is already done** — just needs legacy snooze triggers flipped. Francisco targeted "Tue/Wed next week" = **today/tomorrow**; unconfirmed. [Thread (Jul 30)](https://avantstay.slack.com/archives/C0B1SK218F7/p1785419807377679) | Francisco / Igo |
| 🔴 Blocked – eng | Document Builder field gaps | Neighborhood Characteristics ([BODI-3141](https://linear.app/avantstay/issue/BODI-3141/be-add-neighborhood-characteristics-fields-to-document-builder)), sq ft (BODI-3200), Amenities ([BODI-3452](https://linear.app/avantstay/issue/BODI-3452/fe-add-amenities-section-to-document-builder)) — none shipped | Rodrigo / Francisco |
| 🔴 Blocked – eng | Slack escalation — Oregon Coast | Needs `fo_slack_pilot_enabled = true` flipped in `region_slack_channel_map`; Desiree can't self-serve | Eng (unassigned) |
| 🔴 Blocked – eng | Performance/alerts dashboard bugs | Duckie-side, recurring ("See Run" button broke Jul 28) | Amitoj / Justin |
| 🔴 Blocked – eng | Stay-status gap | ~20% of ZD tickets lack a stay status; not configured for inquiry-only tickets | Igo |
| 🟠 Blocked – ops | QA dashboard + category targets | Scores, markdowns, handoff trends, per-category goals — feeds the weekly "which categories to 80%" question | Ashrit |
| 🟠 Blocked – ops | Vrbo forwarded-email filtering | Vrbo duplicates showing as "no reply," inflating that metric | Cameron / Ashrit |
| 🟠 Blocked – ops | Concierge use case | Flagged Jul 30; confirmed not yet scoped | Cameron |
| 🟠 Blocked – ops | Service Animal VAS auto-creation | Suggested Jul 29, not urgent, not formally scoped | Desiree |
| ⚪ For awareness | AI agent cost / contract negotiation | ~300 tickets/day corrected volume (~$5K/mo); refining numbers before annual contract | — |
| ⚪ For awareness | Centralized guest communication | Comms flagged as disjointed across channels; longer-term goal, no owner yet | — |
| ✅ Resolved | Taxonomy V3 race condition | Skip-tag check (~Jul 22–27) + deployment delay (Jul 28) — residual <1% closed | Francisco |
| ✅ Resolved | Zendesk 404 alert noise | Quieted 404s from `#duckie-avantstay-errors-external` (Aug 3) | Justin |

---

## Key owners

| Person | Role |
|---|---|
| Cameron Tabucchi | AvantStay PM — roadmap, guardrails, strategy |
| Ashrit Kamireddi | AvantStay — analytics, spend, prioritization |
| Francisco Serna | AvantStay eng — MCP, Zendesk, tools, alerting |
| Desiree Saloma | AvantStay GX — ticket auditing, flagging issues |
| Igo Brilhante | AvantStay eng — ECI tools, booking status, DB |
| Rodrigo Palhares | AvantStay eng — ECI, LCO endpoint, ISGI `createCase`, contact-update fix |
| Mateus Tavares | AvantStay eng — booking status, Taxonomy V3 |
| Wanderson Jesus | AvantStay eng — ops-side ISGI logic |
| Raneem El Torky | AvantStay — case automation, taxonomy |
| Justin Missmahl | Duckie — Slack integration, workflows, alerting |
| Amitoj Singh | Duckie — performance dashboard |
| Jolee VanLeuven | AvantStay Field Ops |
| Reuben Doetsch | Stakeholder — pushing 60% goal |
| Matt Garza | AvantStay IoT — lock/ECI updates |
