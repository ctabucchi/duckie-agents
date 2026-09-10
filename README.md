# duckie-agents

Design, runbooks, and rollout notes for AvantStay's [Duckie](https://app.useduckie.ai) agents (GX / Guest Experience).

> **Private repo — contains internal identifiers** (Zendesk group & field IDs, Voyage property UUIDs, Duckie object IDs, staff names, SOP links). Do not make public without sanitizing.

---

## Agents

### [Guest Registration Outreach](guest-registration-agent-DRAFT.md) — Task #1: Age Verification

Proactive, property-triggered pre-arrival outreach. On a new **Airbnb** reservation at **Island Retreat** (Port Aransas) or **Charming Way** (Emerald Coast 30A), confirm the **primary guest is 25+** (strict HOA rule) before the stay.

**Status: built, in testing** (all Duckie objects `draft`; all 3 deployments `Testing` + `Paused` — nothing live).

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

Files:
- [`guest-registration-agent-DRAFT.md`](guest-registration-agent-DRAFT.md) — agent config, deployments, rollout status, open items
- [`guest-registration-runbook-DRAFT.md`](guest-registration-runbook-DRAFT.md) — the per-ticket runbook (design copy; live version in Duckie is source of truth)
- [`guest-registration-property-config-TABLE.md`](guest-registration-property-config-TABLE.md) — the phase-2 scaling layer (property → requirement mapping)

FigJam: <https://www.figma.com/board/Z1xOQBgpCHA2PkgVFVrJhC/Duckie-agent-for-reservation-Zendesk-comments>

---

## Roadmap & context

- [`duckie_roadmap.md`](duckie_roadmap.md) — overall Duckie roadmap
- [`ECI_LCO_Duckie_Matrix.xlsx`](ECI_LCO_Duckie_Matrix.xlsx) — ECI / LCO automation matrix
- Weekly syncs: [2026-07-14](duckie_weekly_sync_2026-07-14.md) · [2026-08-04](duckie_weekly_sync_2026-08-04.md) · [2026-08-24](duckie_weekly_sync_2026-08-24.md)

## Reference

- [`duckie-mcp-write-setup.md`](duckie-mcp-write-setup.md) — how the write-scoped MCP connection is configured
- [`duckie-case-creation-instay-vs-feedback-instructions.md`](duckie-case-creation-instay-vs-feedback-instructions.md)
- [`duckie-reservation-outreach-agent-instructions.md`](duckie-reservation-outreach-agent-instructions.md)
- [`duckie_renewal_talking_points.md`](duckie_renewal_talking_points.md)
- [`cowork_context.md`](cowork_context.md)

---

## Local setup

`.mcp.json` is **git-ignored** because it holds a live Duckie API key. To work with the write MCP locally, create `.mcp.json` in the repo root:

```json
{
  "mcpServers": {
    "duckie-write": {
      "type": "http",
      "url": "https://app.useduckie.ai/api/mcp",
      "headers": { "Authorization": "Bearer <your dk_live_ key>" }
    }
  }
}
```

See [`duckie-mcp-write-setup.md`](duckie-mcp-write-setup.md) for how to mint the key.
