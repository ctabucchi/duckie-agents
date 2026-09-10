# Duckie Weekly Sync — July 14, 2026
*Since last sync (Jul 7) · Sources: #proj-duckie-avantstay, #gx-duckie-integration-internal, #duckie-integration-alerts, Linear*

---

## High-Level Updates

**Tagging / Taxonomy V3 (AI-agent-allowed toggle)** — Build was ready by Jul 10. Cutover meeting kept slipping on calendar mixups; invite finally went out this morning, meeting happening today.

**Booking status (IDV / Payment / Guest Agreement)** — Additional booking-status and check-in/access tools shipped Jul 7, per Francisco's recap.

**ISGI case creation** — Still in progress: backend endpoint in review, test/prod endpoints scoped in GX Automations. Cameron flagged this to stakeholder Reuben (Jul 13) as one of two main blockers to the new 60% full-resolution goal.

**Slack integration (market channel escalation)** — New failure this week: Duckie's post to Nashville/Asheville market channels failed (bot wasn't in the target channel). Desiree flagged Jul 13; Justin added missing tools and asked her to revalidate this morning.

**Overall resolution rate** — Cameron's Jul 13 update to Reuben: tracking ~30–37% full AI resolution. Duckie phone-support demo happened Jul 9 — good progress, but still a ways from AI answering calls. New team goal: 60% full resolution, with tapechart access and ISGI creation as the two named blockers. Ashrit flagged relocations as the hardest category.

**Zendesk trigger bug (new, resolved)** — A bad trigger caused solved tickets (Duckie- and human-handled) to reopen; initially mistaken for a license issue. Francisco deactivated it night of Jul 12.

**QA / reporting asks (new)** — Desiree requested a QA dashboard (scores, common markdowns, comments) that also covers human-handoff tickets, to understand handoff trends. Ashrit agreed to look into it. Desiree also flagged her Jul 12 QA evaluations vanished from the tool.

**Other** — Hotel use case confirmed live (Duckie answers most hotel written comms). New Linear ticket BODI-3200 (add home square footage to Document Builder) — same class of missing-field gap as last week's Neighborhood Characteristics ticket, assigned to Rodrigo for next sprint.

---

## What We Still Need to Make Progress On

- **Land the AI-agent-allowed cutover** — today's meeting is the moment; Cameron still needs to go through Voyage marking each ticket category yes/no before/at cutover.
- **Confirm booking-status fields are live** and get runbook/prompt logic wired to them — no confirmation yet this happened.
- **Unblock ISGI creation and tapechart access** — the two named blockers to the 60% goal; no new movement on either this week.
- **Verify the Nashville/Asheville Slack fix holds** on the next real ticket, and get a status check on the Big Bear trigger issue (unmentioned this week — may still be stalled since 7/3).
- **Resource the QA dashboard ask** and fix the missing Jul 12 evaluations.
- **Reconfirm Datadog alert tuning** — #duckie-integration-alerts is still firing frequently; unclear if the "only ping on real concerns" ask ever got actioned.
