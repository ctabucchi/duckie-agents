# Instructions to paste into the Duckie chat assistant

Update the ISGI Case Creation workflow/agent logic (the `createCase` MCP tool flow, live since Jul 27) so it sets the correct **case type** — either "In-Stay Guest Issue" or "Feedback" — instead of always creating cases as In-Stay Guest Issue. Duckie should still create a case in both scenarios; only the case type should change.

**The distinction**

1. **In-Stay Guest Issue** — case type = In-Stay Guest Issue.
   Guest is currently in-house (mid-stay, not checked out) and is reporting a problem that needs operational action during their stay.
   Example: "The pool is dirty, can someone come clean it?"

2. **Feedback** — case type = Feedback.
   Guest is checking out or has already checked out, and is simply letting us know about something they noticed — not requesting in-stay action.
   Example: guest says "We are out," then mentions a broken umbrella and a pool skimmer issue. This is FYI, not an active request.

**Real example of the bug — Ticket 4904164 (2026-08-04)**
Guest wrote "We are out. Thanks!! ... This umbrella was broke when we got here. The pool skimmer got taken out and we couldn't get it back in," followed by "The front door isn't locking."
Duckie opened three separate cases (umbrella, pool skimmer, door lock) all typed as In-Stay Guest Issue, and flagged the door as an "urgent security issue requiring immediate follow-up." Since the guest had already checked out and was just informing us, all three cases should have been created with case type = Feedback instead.

**Logic to add**
- Check guest stay status (in-house vs. checked out) before setting case type. If the guest indicates they've checked out or are checking out (e.g., "we are out," "just left," checkout-day message with no ETA back), default the case type to Feedback rather than In-Stay Guest Issue.
- This applies even to issues with safety/security implications (e.g., a lock not securing the unit) — still create the case so ops is alerted, just typed as Feedback rather than In-Stay Guest Issue, since the current guest isn't the one needing active help.
- One guest message with multiple issues should still be split into multiple cases (this part is already working and should stay as-is) — just gate each split issue through the in-stay-vs-feedback check individually to set its case type, since a single message can mix both types.

**When to ask a clarifying question before creating the case**

For in-stay issues (case type = In-Stay Guest Issue) that don't sound urgent, Duckie should not assume the guest wants someone dispatched right away. Before creating the case, ask the guest: do they want someone from the local team to come by now, or are they okay waiting until checkout for someone to address it?

- This applies when the guest is still in-house and the issue isn't urgent/active (nothing unsafe, nothing actively disrupting the stay right now) — e.g., a stain, an odor, cosmetic damage, something noticed but not currently causing a problem.
- Create the case either way, once the guest answers — but include the guest's answer in the case so the local team knows whether it's "come now" or "no rush, fine until checkout." That context matters for how ops prioritizes and schedules the visit.
- This is different from truly urgent in-stay issues (e.g., no A/C in extreme heat, a broken lock, no hot water) — for those, don't ask, just create the case and get someone out.

**Real example of the gap — Ticket 4899857 (2026-08-03)**
Guest (Lori) messaged that she found a dry dog urine stain/odor in the master bedroom, pulled out from under an ottoman — dry, not an active mess, nothing urgent. Duckie created the ISGI case immediately and logged it with the local team, who then dispatched maintenance to arrive in 10–15 minutes without first checking whether the guest wanted someone to come right away or was fine waiting until checkout. Since the stain was dry and not disrupting the guest's stay, Duckie should have asked Lori first, then passed her preference along with the case.

**Test before going live**
- Re-run ticket 4904164 through the updated logic and confirm all 3 cases are created with case type = Feedback instead of In-Stay Guest Issue.
- Spot-check a handful of recent tickets where the guest was still in-house and reporting a live issue (e.g., "pool is dirty, please clean") to confirm those still correctly get case type = In-Stay Guest Issue.
- Re-run ticket 4899857 through the updated logic and confirm Duckie asks the guest whether she wants someone to come now vs. wait until checkout, and that the case is created with her answer included as context for the local team.
- Spot-check a genuinely urgent in-stay issue (e.g., broken lock, no A/C) to confirm Duckie still creates the case immediately without pausing to ask.
