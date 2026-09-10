# Duckie MCP — enabling Core objects write

## What I found

- Duckie exposes **one** MCP endpoint (`https://app.useduckie.ai/api/mcp`). You authenticate with
  **either** OAuth (the Claude "custom connector" flow) **or** a **customer API key** (bearer token).
- **Scopes are only selectable when you create a customer API key.** The "Core objects write"
  scope is a checkbox in Duckie's *Create API key* dialog. It is **not** offered by the Claude
  OAuth connector flow — Claude's connector requests a fixed scope set that doesn't include it,
  and Duckie has no UI to grant it out-of-band or to manage/revoke MCP OAuth grants. That's why
  "Core objects write doesn't show up" no matter how many times you disconnect/reconnect the
  OAuth connector. Signing out of the browser won't change it either.

## The fix: use the bearer API key, not OAuth

A write-scoped key already exists (created 2026-09-09):

- **Name:** `Duckie MCP Key (core write)` · **Preview:** `dk_live_6d1..._UE`
- **Scopes:** all reads + **Core objects read + Core objects write**
- **Expires:** never · Settings → API & MCP in Duckie

### Steps

1. **Get the key value.** It was copied to your clipboard when created. If the clipboard's been
   overwritten, the full secret is no longer retrievable — delete `Duckie MCP Key (core write)`
   in Duckie → Settings → API & MCP and create a new one (Create key → check **Core objects
   write** under "Duckie Assistant MCP" → No expiration).

2. **Put it in the project MCP config.** I created `.mcp.json` in this project
   (`/Users/cameroncameron/Documents/Claude/Projects/Duckie Roadmap/.mcp.json`) with a
   placeholder. Replace `PASTE_YOUR_dk_live_KEY_HERE` with the real key:

   ```json
   {
     "mcpServers": {
       "duckie-write": {
         "type": "http",
         "url": "https://app.useduckie.ai/api/mcp",
         "headers": { "Authorization": "Bearer dk_live_...." }
       }
     }
   }
   ```

3. **(Optional) turn off the OAuth connector** for this project so you don't get two copies of the
   Duckie read tools — in the desktop app's connector settings, disable the existing Duckie
   custom connector (or leave it; duplicates are harmless, just noisy).

4. **Restart Claude Code / start a fresh session** in this project. It will prompt to approve the
   new `.mcp.json` server — approve it. The `duckie_create_core_object` / `duckie_update_core_object`
   / `duckie_delete_core_object` tools will then be available.

5. **Tell me "write tools are live"** in the new session. I'll verify the three write tools are
   present, then build (from the specs in this folder + the project memory):
   - Runbook: **Age Verification Outreach**
   - Agent: **`[DRAFT] Guest Registration Outreach`** (status `draft`), pointing at that runbook

## Security notes

- `.mcp.json` holds the key in plaintext. This project is **not** a git repo, so it won't be
  committed — but don't move it into one without switching to a `${DUCKIE_MCP_KEY}` env-var
  reference (Claude Code expands `${VAR}` in `.mcp.json`).
- The key grants create/update/**delete** on Duckie core objects (agents, runbooks, guidelines,
  guardrails, etc.) for the whole org. Treat it like a production credential. Rotate/revoke it in
  Duckie → Settings → API & MCP if it leaks or once the build work is done.
