# duckie-agents

Design, runbooks, and rollout notes for AvantStay's [Duckie](https://app.useduckie.ai) agents (GX / Guest Experience).

> **Private** — contains internal identifiers (Zendesk group & field IDs, Voyage property UUIDs, Duckie object IDs, staff names, SOP links). Do not make public without sanitizing.

## Docs site

The `docs/` tree is published with **MkDocs Material** to GitHub Pages on every push to `main` (see `.github/workflows/deploy-docs.yml`).

- **Site:** <https://ctabucchi.github.io/duckie-agents/> (repo members only)
- Start at [`docs/index.md`](docs/index.md).

### Run the site locally

```bash
pip install -r requirements.txt
mkdocs serve
```

## Layout

```
docs/
├── index.md
├── agents/
│   └── guest-registration-outreach/   # Task #1: age verification (25+ HOA)
│       ├── index.md                   # agent config, deployments, status
│       ├── runbook.md
│       └── property-config.md
├── roadmap/
│   ├── index.md
│   ├── syncs/                         # weekly sync notes
│   └── assets/                        # roadmap deck + ECI/LCO matrix
└── reference/
```

## Local Duckie MCP

`.mcp.json` is **git-ignored** — it holds a live Duckie API key. To use the write MCP locally, create it in the repo root:

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

See [`docs/reference/mcp-write-setup.md`](docs/reference/mcp-write-setup.md) for how to mint the key.
