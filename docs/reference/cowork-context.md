# Claude Session Context for Cowork

## Identity
- **Model**: Claude Sonnet 4.6
- **Interface**: Claude.ai (web/mobile/desktop chat)
- **Current Date**: Monday, June 22, 2026
- **User Location**: Reno, Nevada, US

---

## Connected MCP Servers
| Name | URL |
|------|-----|
| Duckie | https://app.useduckie.ai/api/mcp |
| Gmail | https://gmailmcp.googleapis.com/mcp/v1 |
| Google Drive | https://drivemcp.googleapis.com/mcp/v1 |
| Slack | https://mcp.slack.com/mcp |

---

## Available Skills
| Skill | Trigger |
|-------|---------|
| `docx` | Word document creation/editing |
| `pdf` | PDF reading, creation, manipulation |
| `pptx` | PowerPoint/slide deck work |
| `xlsx` | Spreadsheet files (.xlsx, .csv, .tsv) |
| `product-self-knowledge` | Anthropic product facts |
| `frontend-design` | UI/visual design guidance |
| `file-reading` | Router for uploaded file types |
| `pdf-reading` | Read/extract content from PDFs |
| `skill-creator` | Create/modify/test skills |
| `cowork-plugin-management:cowork-plugin-customizer` | Customize Cowork plugins |
| `cowork-plugin-management:create-cowork-plugin` | Create new Cowork plugins |

Skills are located at `/mnt/skills/public/<name>/SKILL.md` (or `/mnt/skills/plugins/` for Cowork plugin skills).

---

## Network Configuration
- **Egress**: Enabled with allowlist
- **Allowed Domains** (partial list): `api.anthropic.com`, `github.com`, `pypi.org`, `npmjs.org`, `registry.npmjs.org`, `files.pythonhosted.org`, `raw.githubusercontent.com`, and others.

---

## Filesystem
| Path | Access |
|------|--------|
| `/mnt/user-data/uploads` | Read-only (user uploads) |
| `/mnt/transcripts` | Read-only |
| `/mnt/skills/public` | Read-only |
| `/mnt/skills/private` | Read-only |
| `/mnt/skills/examples` | Read-only |
| `/home/claude` | Read/write (working directory) |
| `/mnt/user-data/outputs` | Read/write (final deliverables) |

---

## Anthropic API in Artifacts
Artifacts can call the Anthropic API directly:
- **Endpoint**: `https://api.anthropic.com/v1/messages`
- **Model**: `claude-sonnet-4-6`
- **Max tokens**: 1000
- No API key needed (handled automatically)
- Supports: MCP servers, web search tool, file uploads (PDF/image as base64), multi-turn conversation history

---

## Key Behavioral Notes
- Always read relevant `SKILL.md` before creating files or writing code
- Final output files go to `/mnt/user-data/outputs/`
- Working/temp files go to `/home/claude/`
- Use `present_files` tool to share files with user
- Knowledge cutoff: end of August 2025; search the web for anything more recent
- Copyright: paraphrase sources; max 15-word quotes; one quote per source

---

## Available Tools (Summary)
- `bash_tool` — run shell commands
- `create_file` / `str_replace` / `view` — file management
- `web_search` / `web_fetch` — internet access
- `image_search` — find images
- `weather_fetch` — current weather
- `places_search` / `places_map_display_v0` — maps and places
- `fetch_sports_data` — live sports scores/stats
- `visualize:show_widget` — inline SVG/HTML visuals
- `recipe_display_v0` — interactive recipes
- `ask_user_input_v0` — present options to user
- `message_compose_v1` — draft emails/messages
- `conversation_search` / `recent_chats` — search past chats
- `memory_user_edits` — manage persistent memory
- `tool_search` — load deferred tools (Google Drive, Slack, Gmail, Zendesk, etc.)
- `search_mcp_registry` / `suggest_connectors` — discover MCP connectors
- `present_files` — share files with user
- `end_conversation` — last-resort conversation ending
