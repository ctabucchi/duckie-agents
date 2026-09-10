# Duckie × AvantStay Roadmap
**Updated from Slack + Jul 7 weekly sync · Week of July 7, 2026**
*Sources: #proj-duckie-avantstay · #gx-duckie-integration-internal · #duckie-integration-alerts · "GX AI/Duckie Roadmap sync weekly" (Jul 7, 12:30pm PDT — Cameron, Ashrit invited, Francisco + Nicole attended)*

---

## THIS WEEK — Focus Areas

### 1. Correct Tagging + Progress 🔴 High Priority
Two live tagging failures this week, both causing Duckie to act when it shouldn't have.

**VIP tagging gap:**
- Ticket 4474293 was never tagged VIP, so Duckie answered it — and answered incorrectly (Jul 4, Cameron).
- Root cause (Igo, Jul 4): buyout homes aren't inheriting the VIP tag from the underlying property.
- Linear [BODI-3136](https://linear.app/avantstay/issue/BODI-3136/review-missing-vip-tag-for-home-and-booking) opened: review missing VIP tag for home/booking, fix buyout inheritance.
- Still blocked on Taxonomy V3 (Igo/Francisco) for broader tag precision — carried over from last week, no new timeline given.
- **Update from Jul 7 sync:** Taxonomy V3 has a concrete shape now. Francisco is building an "AI agent allowed" / "AI agent not allowed" checkbox in Voyage, toggled per ticket category — this replaces the current tag list Duckie's deployment matches against. Francisco is testing now and hopes to push live as soon as tomorrow (Jul 8), but wants a coordinated live sync with Cameron first (not a silent deploy) since it requires Duckie's deployment config to switch from "must include any of these category tags" to "must include AI-agent-allowed tag." Also decided: pass Duckie the **full tag list**, not a filtered subset — gives it what it needs to make nuanced calls like VIP, closing the gap behind the 4474293 incident directly.

**Resolution rate — investigated, not a regression:**
- Ashrit flagged a weekend dip from the 38% peak (Jul 6). Cameron's full breakdown (Jul 6): no broad reliability drop — it's ticket-mix driven. 7/5 hit a new peak (38.4%). 7/3 was a heavy check-in day (45% of volume vs. ~22-25% normal) and access/lockbox-code questions resolved at just 8% that day, dragging the average.
- Consistently weak categories: In-Stay Service/maintenance (10%), Lost & Found (11%), ID Verification (8%), Reservation Alterations (11% despite 45% first-reply). Strong categories: Age (72%), Pets (50%), Discounts (38%).
- Reuben Doetsch (stakeholder) wants resolution rate pushed to 50%.
- **Note:** the weak categories (ID Verification, lockbox/access) line up directly with the booking-status item below — fixing that should lift resolution rate on its own.

**Data pipeline tag corruption (resolved):** GX datawarehouse tags briefly arrived as one semicolon-joined string instead of an array (6/30–7/2), breaking all tag-based lookups and zeroing out resolution-time metrics. Root cause: a Postgres migration broke `tags` formatting and dropped `zendesk_metric_set`. Fixed and backfilled by Gabriel Matheus de Oliveira (Jul 2) — no catch-up lag, safe to trust current data.

**Status:** VIP fix in progress (BODI-3136). New AI-agent-allowed tagging system in active testing, targeting live push Jul 8 pending a coordinated deploy sync. Resolution rate healthy, no fire needed — lever is lockbox/ID-verification categories. Data pipeline issue closed.

**Before the cutover, Cameron needs to:** go through Voyage and mark each ticket category yes/no for AI-agent-allowed (currently defaults to all-on, which isn't the intent) — this is the actual configuration step behind the tagging fix, not just an eng build.

---

### 2. Duckie Understanding Booking Status (IDV / Payment / Guest Agreement) 🔴 New — High Priority
Core incident (Cameron, Jul 4): Duckie gave check-in info without knowing the guest hadn't completed IDV ([ticket 4776339](https://avantstay.zendesk.com/agent/tickets/4776339)). Ashrit: "How is it otherwise responding when someone asks about check-in instructions" if it can't see IDV/payment/agreement status.

**Root cause (Igo, Jul 4):** Duckie is exposed to raw Voyage booking-status fields, not guest-facing status. A booking can show "Booked" while IDV or payment is still pending — Duckie has no way to distinguish that from the raw field. Same root issue affects ECI: the `getEciRequestStatus` tool returns Confirmed/Canceled but never flags outstanding IDV, so Duckie can confirm an ECI without warning the guest they still need to complete verification.

**Actions:**
- Linear [BODI-3135](https://linear.app/avantstay/issue/BODI-3135/use-guest-facing-reservation-status-in-duckie-integration) opened: expose guest-facing reservation status to Duckie (same terminology/logic as Booking Hub), not raw Voyage data. Owners: Igo, Mateus Tavares, Rodrigo Palhares.
- Ashrit's ask: this should be deterministic (a waterfall/decision tree Duckie follows), not a judgment call Duckie makes on its own — "I generally don't like Duckie making these decisions."
- Interim mitigations already shipped: Cameron restricted lock-code troubleshooting to no backup-code hand-out until this is fixed; Desiree is having ECI confirmations include the Booking Hub link so guests can self-check IDV status.
- **Implementation plan locked in at Jul 7 sync:** Francisco will add Paid / IDV / Guest Agreement as explicit status fields to the document builder (or a dedicated tool) — framed as a "checking readiness"-style field per status (e.g. "Is IDV completed? Yes/No"). When a status is incomplete, Duckie should use guest-facing language rather than a raw status dump — e.g. "it looks like you haven't completed IDV yet, you can do that in your Booking Hub." Francisco will flag Cameron once the fields are live so Cameron can wire the check into the relevant runbooks/prompts (likely the lock-code flow first). This also directly fixes the ECI gap: a guest can have ECI approved while IDV is still outstanding, and Duckie currently has no way to catch that.

**Status:** Root cause diagnosed, eng ticket open (BODI-3135), interim guardrails live, implementation plan agreed Jul 7. Not yet built — next concrete step is Francisco shipping the document-builder fields, then Cameron wiring runbook logic once available. **Sequencing decision (Jul 7):** the team explicitly agreed to finish tagging + this booking-status work *before* starting ISGI case creation (see below), to avoid three half-finished projects at once.

---

### 3. ISGI (In-Stay Case) Creation 🟡 Scoping
When a guest reports an in-stay issue, Duckie should create an ISGI directly (via Remy or equivalent) instead of requiring a human to do it.

**Progress since last update:**
- Raneem El Torky (GX case automation) confirmed this aligns with their roadmap: after automating case creation in Fresh/Voyage/Zendesk, the plan is to pilot automated case creation directly from AI agents (Duckie) and SpokesPhone, using one standardized taxonomy across platforms. She's open to piloting Duckie in parallel with the Fresh market pilot and will QA both.
- Francisco needs the required-fields list to build the ISGI-creation MCP endpoint for Duckie; pulled initial context from #proj-voyage-case-classification.
- Cameron reiterated (Jul 6, to stakeholder Reuben) this is one of the two next big integration points, alongside the Slack integration below — expected to meaningfully improve local-team response time and let Duckie "fully resolve" more in-stay volume.
- **Jul 7 sync:** Cameron pushed again for movement; Francisco confirmed no dev work has started on Ashrit's/GX ops side and will ask directly in the operations Slack channel whether anything has been built. Team explicitly decided to sequence this **behind** the tagging and booking-status work above — deliberate prioritization, not neglect, so the team isn't juggling three half-finished builds at once.

**Status:** Scoping, intentionally paused behind tagging + booking-status work. No committed build date yet — next unblock is Francisco's ops-channel check on whether any dev work has quietly started.

---

### 4. Slack Integration — Duckie → Market Channel for Unanswerable Property Questions ⚡ In Active Testing
Phase 1: guest gets a warm hold reply, Duckie posts a structured message to the correct market Slack channel (property, question, what systems returned, ZD ticket #), then hands the ticket to GX. Phase 2 (later): take the market team's Slack reply and write it back to the guest directly.

**Progress since last update:**
- Market Slack channel ID list delivered (Cameron, Jun 30) plus a channel-owner mapping (Francisco, Jul 1).
- Duckie team (Justin Missmahl) confirmed this only needs a workflow, not a second agent — built against an agent table mapping region → Slack channel (a few entries still incomplete, need review).
- Testing moved from the generic test channel to a real market: Big Bear (`#bigbear-market`, tag `big_bear`) as of Jul 3.
- **Blocker:** Cameron hit a snag getting a ticket to trigger the workflow in Big Bear (Jul 3); flagged to Joel/Justin. As of Jul 6 follow-up, still "not yet" resolved — no movement in 3 days.
- Phase 2 (Slack reply → Zendesk) is scoped as roughly a day of work once Phase 1 is verified; main complexity is knowing not to reply if a human has already handled the conversation.
- Related: Desiree/Francisco found Duckie is missing some Voyage fields it should already have (Home Manual gaps, Neighborhood Characteristics) — several of the Slack-escalation test cases were guest questions Duckie should've answered from Voyage directly. Linear [BODI-3141](https://linear.app/avantstay/issue/BODI-3141/be-add-neighborhood-characteristics-fields-to-document-builder) opened to add Neighborhood Characteristics to the document builder; most Home Manual fields are already available and just need to be inserted.

**Status:** Config built and live for one test market; blocked on trigger issue in Big Bear. Cameron reiterated the push at the Jul 7 sync but no new movement reported — still stalled since 7/3.

---

## ORG NOTE — Duckie Team Change

**Joel Ritossa has left Duckie.** Raised at the Jul 7 sync — Joel was AvantStay's main day-to-day Duckie contact (platform, guardrails, MCP, voice strategy) and the two teams had a daily working cadence with him. Francisco and Cameron discussed the risk to roadmap continuity but decided to keep executing the current plan (tagging → booking status → ISGI cases) regardless. Justin Missmahl appears to be the most active remaining Duckie-side contact (Slack integration, and per today's sync he also recommended splitting the combined ECI/LCO runbook into two separate runbooks — not yet actioned). Worth watching whether Duckie's build pace holds without Joel; Cameron noted he'd started "shopping around" other options given how much of the original pitch was built on that direct-access relationship.

---

## OTHER FOLLOW-UPS FROM SLACK (not previously on roadmap)

### Voice Agent Demo — Scheduling
Francisco offered demo slots (Jul 7, 11:30am or 12pm PST; Jul 8, 9am PST). Latest reply suggests **Jul 8, 9am PST** works — needs final confirmation with Justin Missmahl.

### Assistant Timing Out on Updates — Patched
Desiree flagged the Assistant consistently timing out / not posting updates since the weekend of 6/28 (Jun 30). Duckie team diagnosed it as a mis-prompt rather than a true timeout and shipped a new handler system for this class of issue (Jul 4). Duckie team is now moving on to the Slack/Zendesk handoff system next — worth confirming with Desiree that the timeout issue hasn't recurred.

### Performance Tab Bug
Cameron flagged a Duckie performance-tab page that won't load (Jul 6, screenshot shared). Amitoj Singh is on it "at top priority," no ETA yet.

### Lock Code SOP — Mid-Weekend Redirect
Cameron had to redirect the lock-code SOP mid-weekend (context behind part of the 7/3 resolution dip above) — full recap folded into the resolution-rate analysis; no separate action needed but flagging for visibility since it wasn't previously logged.

### NPS Survey Relaunch — Reach Gap for International Guests
A new NPS survey quietly went live last week with no formal announcement (confirmed by Francisco, Jul 7 sync). Gap identified: guests with international phone numbers (e.g., Miami hotel guests) often aren't reachable by SMS survey. Fix planned: WhatsApp Business integration, targeted 2–3 months out. Adjacent to Duckie but affects the same guest-comms surface — flagging for visibility, not a Duckie action item.

---

## NEXT — Prioritized Pipeline (carried over, no update this week)

### Availability Checks
Requires tapechart MCP tool. **Status:** Blocked — Igo/Francisco.

### VIP Ticket Expansion
~2% resolution on VIP tickets, compounded by the tagging gap above. **Status:** Blocked on Taxonomy V3 + VIP tag fix (BODI-3136).

### Alerting & Monitoring ✅ Live
`#duckie-integration-alerts` is live and flowing (Datadog monitors on GX Support API error rates). Cameron asked Francisco (Jun 26) to only ping the channel on real concerns rather than every recovery/warn pair — worth confirming that tuning happened, since the channel is still posting both Warn and Recovered messages for every blip.

### Historical Evaluation Data ✅ Complete

---

## LATER — Exploring / Future (carried over, no update this week)

- True Resolution Volume — Data Cleanup (no clear owner yet)
- Proactive Comms (~642 tickets/mo, unscoped)
- Local Team Slack Handoffs (unscoped)
- Auto-Create VAS in Voyage from Duckie (Wanderson to weigh in)
- LTR Booking Flow (David Taylor-Smith, early planning)
- Re-trigger Duckie After Human Passoff (needs scoping with Joel)

---

## Open Bugs / Flags

| Issue | Flagged By | Status |
|-------|-----------|--------|
| Duckie can't verify IDV/payment/guest-agreement status before answering check-in questions | Cameron (Jul 4) | Root cause known — BODI-3135 open |
| VIP tag missing on buyout homes → Duckie answers VIP tickets it shouldn't | Cameron (Jul 4) | Fix in progress — BODI-3136; new AI-agent-allowed tagging (targeting Jul 8) should close this class of gap |
| Slack-to-market-channel trigger not firing in Big Bear test | Cameron (Jul 3) | Stalled since 7/3 — needs push |
| Duckie performance tab not loading | Cameron (Jul 6) | In progress — Amitoj, top priority |
| GX datawarehouse tags/metrics corrupted (6/30–7/2) | Ashrit (Jul 2) | ✅ Fixed + backfilled |
| Assistant timing out on updates | Desiree (Jun 30) | ✅ Patched — confirm no recurrence |
| Airbnb/VRBO guardrail applying to confirmed bookings | Desiree (Jun 17, Jun 30) | Carried over — needs Joel fix |
| Reason for Stay / Excited for Stay timing issue | Desiree (Jun 24) | Carried over — awaiting delay fix |

---

## Key Owners

| Person | Role |
|--------|------|
| Cameron Tabucchi | AvantStay PM — roadmap, guardrails, strategy |
| Ashrit Kamireddi | AvantStay — analytics, spend, prioritization |
| Francisco Serna | AvantStay eng — MCP, Zendesk, tools, alerting |
| Desiree Saloma | AvantStay GX — ticket auditing, flagging issues |
| Igo Brilhante | AvantStay eng — ECI tools, booking status, DB |
| Rodrigo Palhares | AvantStay eng — ECI tools |
| Mateus Tavares | AvantStay eng — booking status |
| Wanderson Jesus | AvantStay eng — Voyage/VAS automation |
| Raneem El Torky | AvantStay — case automation (ISGI/Fresh/Voyage), taxonomy |
| Gabriel Matheus de Oliveira | AvantStay data eng — datawarehouse pipeline |
| Ana Paula Tarchetti | AvantStay data — datawarehouse |
| Joel Ritossa | Duckie — platform, guardrails, MCP, voice (**departed Duckie**, per Jul 7 sync) |
| Justin Missmahl | Duckie — Slack integration, workflows; most active remaining Duckie contact post-Joel |
| Amitoj Singh | Duckie — performance dashboard |
| Valerie Li | Duckie — voice product |
| Reuben Doetsch | Stakeholder — pushing resolution rate to 50% |
| Matt Garza | AvantStay IoT — lock/ECI same-day updates |
| Nicole Perry | AvantStay GX lead — reservations, VIP |
| David Taylor-Smith | AvantStay — LTR flow |
