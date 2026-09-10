# Duckie Weekly Sync — August 24, 2026
*Since last sync (Aug 4) · Heavy focus on last 5 days (Aug 19–24) · Sources: #proj-duckie-avantstay, #gx-duckie-integration-internal, Desiree DM, Aug 19 team sync notes, Aug 20 & 21 Cameron/Lina 1:1 call notes, Duckie QA tracker*

---

## Headline: Lina Nguyen joined the project (Aug 19)

Ashrit pulled Lina Nguyen (Product) onto Duckie full-time on Aug 19 to help drive momentum — initially reservation categories (discounts, night extensions) and pushing a few categories to 80–90% resolution. She ramped fast:

- Got herself added to all recurring Duckie syncs (internal + vendor) within 48 hours
- Ran two working sessions with Cameron (Aug 20 evening, Aug 21 midday) — notes below
- Is actively auditing/rewriting runbooks, running property-info audits via **Chef**, and testing **Remy** for direct Voyage updates
- Now driving most of the "Chef" ad-hoc reporting queries in the internal channel (silent handoffs, resolve rates, market breakdowns, missing-field audits)

---

## Key metrics

| Metric | Value |
|---|---|
| Resolution rate | 41.3% (Aug 3) → **45% record** (Fri Aug 21) → dropped to **32%** as of Aug 24 (Ashrit flagged same-day; unclear yet if real regression from weekend changes or a data issue — being investigated) |
| Team goal | 60–70% resolution (Lina's target, set Aug 20 call) — up from the prior 60% framing |
| Silent handoffs (30-day, per Chef audit Aug 21) | 42.3% of handoffs (3,454 of 8,171) had no guest-facing acknowledgment; median blind wait 15.2 hrs, p90 40.6 hrs — flagged as an open problem, runbook fix pending |
| QA tracker baseline (Jul 25–Aug 24, 30 days) | 13 confirmed Duckie errors (property facts, policy, missed questions, dead-end verification) found by new **Duckie QA — Daily Issues** tracker (Reuben's team + Chef). **0 new confirmed issues** in the most recent 48h window (Aug 22–24), though that window was dominated by the Tahoe outage, which Duckie reportedly handled correctly (escalated, didn't invent property facts) |

---

## Cameron / Lina call notes — key takeaways

**Aug 20 (6:01pm ET) — "Ducky Strategy & Roadmap"**
- Set the 60–70% resolution target and agreed the roadmap needs real structure — Cameron flagged there's been no dedicated PM support navigating Voyage/Zendesk/Duckie, mostly reactive dev help
- Root-caused dashboard inaccuracy: Airbnb/Vrbo threads get miscategorized ("human-replied" when it was actually Duckie; "open" when actually on hold) — deflating the real resolution number
- Reviewed a parking-inquiry ticket where Duckie handed off unnecessarily — reinforced that Duckie needs direct property-info + reservation-management data access rather than relying on runbooks alone
- Cameron: after direct coaching in the Duckie chatbot UI, it holds the fix ~90% of the time on similar future tickets
- Retro on the Slack market rollout: rolling out to all markets at once was a mistake — going forward, pilot one channel before broad release
- **Next steps assigned:** Lina — build a shared prioritization doc, test Chip↔Duckie MCP connection for discounts, build an automation roadmap. Cameron — share existing roadmap + auditor task list, add Lina to the Duckie cloud project, coach the chatbot on parking info, update the Slack runbook to always include property name/unit, request interactive back-and-forth Slack replies from the vendor team, review the ETI matrix for Remy automation potential

**Aug 21 (4:04pm ET) — working session**
- Broader theme: pushback on "we've always accepted this inefficient process" thinking (OTA dependencies, Booking.com gaps, etc.)
- Lina hit a chatbot bug live — mid-runbook-edit it picked the wrong file and mangled formatting into HTML; Amitoj (Duckie) jumped on a huddle and fixed it in ~10 min. Root cause per Amitoj: overly long runbooks/chat histories degrade LLM response quality — **recommendation to split large runbooks into smaller files**
- Flagged the human-handoff queue has no urgency differentiation (a broken chair and a house fire get equal priority) — no headcount to monitor it properly. Interim fix already running: Chef pulls in-stay issues hourly, ranks by criticality into a tracking sheet for whoever's on shift
- Property-info gaps (golf carts, beach items, appliances) are forcing avoidable escalations to local teams — Lina kicked off a Port Aransas audit via Chef with a plan to bulk-update Voyage via Remy
- Debated who should be allowed to execute bulk Remy updates — Lina wants leads only, worried about unverified data from offshore auditors
- **Decisions:** Remy approved for open-text property updates (no need to pre-build discrete Voyage fields); Lina pilots the property-info update process, then hands to Desiree once repeatable

---

## Shipped since Aug 4

| Item | Shipped | Detail |
|---|---|---|
| Contact-details reverification step removed | Aug 19 | Root cause of a chunk of the "not 100%" contact-info gap; workflow now confirms directly instead of re-verifying via API |
| Document Builder new fields | Aug 19 | Laundry section, sq footage, property nuances, custom features, cross streets, selling points, distance to downtown, grouped amenities — all now ingestable by Duckie |
| Urgency classification view (ZD) | Aug 21 | AI model tags danger/critical in-stay tickets; live view for GX leads/GOS/Leadership in Zendesk |
| Duckie QA — Daily Issues tracker | Aug 24 | New human-reviewed error tracker (Reuben's team), posts a daily Slack digest of confirmed Duckie mistakes |
| Availability MCP tool (new-reservation data) | Aug 24 | Released today per Francisco; pricing + discount tools targeted next |
| Reporting cadence | Aug 19 | Set to biweekly (down from weekly), per Aug 19 sync decision |

---

## Status board

Per the Aug 4 convention (Ashrit's ask): Eng-blocked split from Ops/Process-blocked, plus a Resolved bucket.

| Status | Item | Detail | Owner |
|---|---|---|---|
| 🔴 Blocked – eng | Silent handoff acknowledgment | 42.3% of handoffs leave the guest blind, no "connecting you to a specialist" message — Lina flagged runbook needs updating; even a first message doesn't solve the underlying 12+ hr sit time | Lina / Cameron |
| 🔴 Blocked – eng | Urgency-based routing/escalation | 55-reply thread since Aug 21 — tagging is live in ZD (Igo), but actual routing/hand-to-local-team-via-Duckie workflow still being designed; Ashrit wants urgent tickets routed to local team via Duckie during regular hours | Igo / Francisco / Lina |
| 🔴 Blocked – eng | Natural disaster / area-wide event comms | Tahoe power outage (Aug 22–23, 50+ reservations) exposed that Duckie isn't trained for "local team has no control" scenarios — created cases + sent tone-deaf messaging. Ashrit proposed (Aug 24): Remy watches a natural-disasters Slack channel, creates a custom-field "event" tied to impacted properties, custom SOP drives Duckie's guest messaging. Not yet built | Ashrit / Cameron / Lina |
| 🔴 Blocked – eng | VIP/Airbnb interaction scope too narrow | Duckie is only allowed to touch VIP + Airbnb bookings for contact-info updates; Lina flagged (Aug 22) it should also answer general questions on those bookings — instruction update needed | Lina |
| 🔴 Blocked – eng | Dashboard miscategorization | Airbnb auto-messages requesting contact info still show as "Human first reply" instead of Duckie-resolved; `duckie_resolved`-tagged tickets still show as open | Ashrit / eng |
| 🔴 Blocked – eng | Runbook formatting bugs in chatbot editor | Editing via the Duckie chat UI can silently pick the wrong runbook / mangle formatting (hit Lina Aug 21, fixed same-day, but a recurrence risk) | Amitoj (Duckie) |
| 🟢 Working on | Runbook audit & split | Lina auditing all runbooks via Chef in batches; splitting long runbooks per Duckie's recommendation (context-window/quality issue) | Lina |
| 🟢 Working on | Port Aransas property-info audit | 123 of 303 active Port A homes (40.6%) missing "Pet Policy" custom field; broader audit underway (parking/layout/pool-spa/pets = top 4 inquiry categories, Port A is #1 market) — feeding a Remy bulk-update pass | Lina / Desiree |
| 🟢 Working on | Chip↔Duckie MCP (discount requests) | Lina testing MCP connection to bring Chip's discount-request skill into Duckie | Lina |
| 🟢 Working on | 24-hour cancellation policy end-to-end | Ashrit push (Aug 24) after a guest waited 6 hrs for something GX already knew — wants runbook + GX SOP updated to honor 24-hr cancellation as courtesy across all states | Ashrit / Lina |
| 🟢 Working on | Case/ticket merge for repeat-contact guests | Duckie should detect back-to-back repeated guest messages and escalate/merge rather than treat as separate — open question on whether dashboard can merge open tickets per guest | Ashrit |
| 🟢 Working on | Age Confirmation Proactive (Port A) | Waiting on process finalization before Desiree executes — open since before Aug 19, no update since | Desiree |
| 🔵 Scoped | Availability / tapechart-driven reservation Q&A | MCP tool for new-reservation availability shipped Aug 24; pricing + discount tools next | Francisco |
| 🔵 Scoped | ETI matrix → Remy automation | Cameron to re-review per Aug 20 call; not yet actioned | Cameron |
| 🟠 Blocked – ops | Linear access for Duckie | Desiree's ask (since Aug 11) to connect Linear so Duckie can create Listing tickets — still pending as of her Aug 19 status | Francisco / Igo |
| 🟠 Blocked – ops | Spam-tag audit | Desiree still auditing; will add findings to deployment skip criteria as identified | Desiree |
| 🟠 Blocked – ops | FO Slack reach-out expansion | Working well for straightforward Q&A; Desiree offered to expand to more markets (Aug 19) — no market named yet | Desiree |
| 🟠 Blocked – ops | Weekly sync restructuring | Lina added to weekly vendor syncs (Aug 24); sync possibly moving earlier by request | Cameron |
| ✅ Resolved | Contact-details reverification bug | Removed reverification step entirely (Aug 19 decision) — contact-details category "improved significantly" per Francisco (Aug 21) | Francisco |
| ✅ Resolved | Case-creation error triage (dedup, Voyage) | Ongoing case-dedup process now catching duplicates (295 since Jul 30, biggest impact Poconos/Port A) | Igo |

---

## Key owners (updated)

| Person | Role |
|---|---|
| Cameron Tabucchi | AvantStay PM — roadmap, guardrails, strategy |
| **Lina Nguyen** | **AvantStay Product — joined Aug 19; runbooks, property-info audits, reservation categories, MCP integrations** |
| Ashrit Kamireddi | AvantStay — analytics, spend, prioritization, urgency/routing |
| Francisco Serna | AvantStay eng — MCP, Zendesk, tools, alerting |
| Desiree Saloma | AvantStay GX — ticket auditing, flagging issues, scheduling |
| Igo Brilhante | AvantStay eng — taxonomy, urgency tagging, dashboard |
| Rodrigo Palhares | AvantStay eng — contact-info verification, integrations |
| Justin Missmahl | Duckie — Slack integration, workflows, alerting |
| Amitoj Singh | Duckie — chatbot/runbook editor, performance dashboard |
| Valerie Li | Duckie — reporting, billing |
| Sarosh Hussain | Duckie |
| Reuben Doetsch | Stakeholder — pushing resolution-rate goal; QA tracker sponsor |

---

## Open questions for Cameron

- Is the Aug 21→Aug 24 resolution-rate drop (45% → 32%) a real regression from weekend changes, or a data/reporting artifact? Ashrit is looking into it — worth a direct check-in.
- Natural disaster / area-wide event workflow: no owner or timeline yet on Ashrit's Remy-based proposal — worth assigning before the next event.
- Silent-handoff runbook fix: flagged by Lina on Aug 21, no visible follow-through yet as of Aug 24.
