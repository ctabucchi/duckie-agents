# Instructions to paste into the Duckie chat assistant

Build a new Workflow agent called **"Reservation HOA Outreach Agent"** that does the following, end to end:

**Trigger**
- Starts when a new Reservation custom object is created in Zendesk.

**Step 1 — Get the data**
- Use the Zendesk custom object tool to read the newly created Reservation object and pull the guest/reservation details needed for outreach (guest contact info, property, dates, HOA requirement fields).

**Step 2 — Send the outreach**
- Send the guest the HOA form outreach message via [CHANNEL — decide: SMS / email / Zendesk ticket reply].
- Message should ask [INSERT EXACT HOA FORM QUESTION(S)].

**Step 3 — Await the answer**
- Wait for the guest's reply on that same channel.
- If no reply within [INSERT TIMEOUT, e.g. 24–48 hrs], [INSERT: send a reminder / escalate to ops / close out].

**Step 4 — Forward the answer**
- Once the guest replies, write the answer back to [DECIDE: the Reservation custom object fields in Zendesk / an internal note on the ticket / an AvantStay backend endpoint].

**Notes for the build**
- This mirrors the original design from Justin Missmahl (Duckie): trigger → get data → send message → await answer → forward answer.
- The Zendesk custom object read tool referenced here is the one Duckie deployed on 2026-07-27 (see workflow example: app.useduckie.ai/build/workflows?id=32b905a2-c16f-42b7-8ff0-dbbbae1fcbdd).
- Francisco Serna's call: lean on Zendesk custom objects instead of a new AvantStay backend endpoint, since Zendesk already has all the needed reservation data — faster to ship, at the cost of being locked into Zendesk's object patterns.
- Test in Testing/no-write-action mode before going live given this writes back to guest-facing channels.

**Open decisions (fill in before/while building)**
1. Outreach channel: SMS, email, or Zendesk ticket reply?
2. Exact HOA form question(s) to send.
3. Where the guest's answer should land (custom object field vs. ticket note vs. backend callback).
4. No-response timeout and escalation path.
