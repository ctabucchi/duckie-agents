# Duckie Agents

Design, runbooks, and rollout notes for AvantStay's [Duckie](https://app.useduckie.ai) agents (GX / Guest Experience).

!!! warning "Internal — do not publish"
    These pages contain internal identifiers (Zendesk group & field IDs, Voyage property UUIDs, Duckie object IDs, staff names, SOP links). The repo and this site are private to repo members.

---

## Agents

### [Guest Registration Outreach](agents/guest-registration-outreach/index.md) — Task #1: Age Verification

Proactive, property-triggered pre-arrival outreach. On a new **Airbnb** reservation at **Island Retreat** (Port Aransas) or **Charming Way** (Emerald Coast 30A), confirm the **primary guest is 25+** (strict HOA rule) before the stay.

!!! info "Status: built, in testing"
    All Duckie objects are `draft`; all three deployments are `Testing` + `Paused` — nothing is live yet.

| Piece | Duckie ID |
|---|---|
| Agent `[DRAFT] Guest Registration Outreach` | `c109c3d5-51e6-4227-9983-a75f1d912066` |
| Runbook `Age Verification Outreach` | `f9765ef6-3d7a-4b69-90eb-7e69ab9d635a` |
| Sweep agent `GX Age Verification — No-Response Sweep` | `2a2cf8b2-b162-4eac-a088-8d825f81b6b9` |
| Sweep runbook `…No-Response Sweep (Scheduled)` | `a3f85c6f-0824-4acd-90da-4df802ee2b33` |
| Deployment `— Send (D1)` | `0e009ffe-c8fc-4646-8e2a-913939fdad9f` |
| Deployment `— Reply (D2)` | `05ebfe54-f29d-4655-9b05-b212a5dd74d0` |
| Deployment `— Sweep (D3)` | `f41107a6-ad07-42db-abc0-819c8fccc7f6` |
| Auditor handoff → Zendesk group | `21008954571028` |

- [Overview](agents/guest-registration-outreach/index.md) — agent config, deployments, rollout status, open items
- [Runbook](agents/guest-registration-outreach/runbook.md) — the per-ticket runbook (design copy; the live version in Duckie is the source of truth)
- [Property config (phase 2)](agents/guest-registration-outreach/property-config.md) — the scaling layer: property → requirement mapping

FigJam: <https://www.figma.com/board/Z1xOQBgpCHA2PkgVFVrJhC/Duckie-agent-for-reservation-Zendesk-comments>

---

## Roadmap & context

- [Roadmap overview](roadmap/index.md)
- Weekly syncs: [2026-08-24](roadmap/syncs/2026-08-24.md) · [2026-08-04](roadmap/syncs/2026-08-04.md) · [2026-07-14](roadmap/syncs/2026-07-14.md)
- [ECI / LCO automation matrix](roadmap/assets/ECI_LCO_Duckie_Matrix.xlsx) (xlsx) · [roadmap deck](roadmap/assets/duckie_roadmap.docx) (docx)

## Reference

- [MCP write setup](reference/mcp-write-setup.md) — how the write-scoped Duckie MCP connection is configured
- [Case creation: in-stay vs feedback](reference/case-creation-instay-vs-feedback.md)
- [Reservation outreach agent instructions](reference/reservation-outreach-agent-instructions.md)
- [Renewal talking points](reference/renewal-talking-points.md)
- [Cowork context](reference/cowork-context.md)
