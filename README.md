# agent-media — Claude Skill plugin

[![npm — mcp-server](https://img.shields.io/npm/v/%40agentmedia%2Fmcp-server?label=%40agentmedia%2Fmcp-server)](https://www.npmjs.com/package/@agentmedia/mcp-server)
[![npm — CLI](https://img.shields.io/npm/v/agent-media-cli?label=agent-media-cli)](https://www.npmjs.com/package/agent-media-cli)
[![Claude plugin](https://img.shields.io/badge/claude-%2Fplugin%20install-A78BFA)](https://github.com/gitroomhq/agent-media)
[![License](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)

AI UGC video production for Claude Code (and any MCP-compatible agent).

Generate photoreal portraits, magazine-style character sheets, lip-synced talking-head videos, and burned-in TikTok / Hormozi captions — all from one Bearer token.

## Install

```bash
claude /plugin install github.com/gitroomhq/agent-media
```

## Auth (first time only)

```bash
npm install -g agent-media-cli
agent-media login
```

`agent-media login` writes a Bearer token to `~/.agent-media/credentials.json`. The MCP server bundled with this plugin reads it via `$AGENT_MEDIA_API_KEY` (set in `.mcp.json`) — or you can paste the `ma_*` token directly into that env var.

## Skills

- `make_portrait` (v1.0.0) — Generate one photoreal portrait. Optionally takes a reference photo (R2-hosted) and a realism preset. Identity is locked from the reference image when provided.
- `make_character_sheet` (v1.0.0) — Generate a magazine-style character sheet from a portrait. Provide EITHER portrait_url (must be R2-hosted) OR portrait_image_base64 (PNG/JPEG, ≤10 MB; the API will upload it to R2 first). Optional ≤10-word description for name/age/vibe hints.
- `make_simple_selfie` (v1.0.0) — Generate a 5/10/15-second vertical UGC selfie video from a character sheet. Subject is framed waist-up, hands free (no object held), TikTok aesthetic, with native lip-synced audio of your script. Script word count is enforced at 2-4 words per second of duration.
- `make_subtitles` (v1.0.0) — Burn TikTok / Hormozi-style captions onto any vNext video (R2-hosted). Auto-transcribes via Whisper when transcript is omitted. Styles: hormozi (default), tiktok, minimal.
- `make_wireframe` (v1.0.0) — Generate a photographic storyboard / wireframe board from a character sheet (R2-hosted) + script. Multi-panel grid showing the same person performing the action progression, 4 / 6 / 8 / 10 numbered panels.
- `make_lip_sync` (v1.0.0) — Bring your own audio: lip-sync a face (an R2-hosted image / character sheet, OR an existing clip) to a provided audio track. No text-to-speech or voice cloning — the character speaks your uploaded recording. Output is a 9:16 talking-head video.
- `make_ugc_video` (v1.0.0) — End-to-end UGC video in one call. Provide EITHER a text description of the person, OR a portrait URL (R2-hosted), OR an uploaded image. The pipeline auto-generates the missing portrait, builds a character sheet, and produces a 5/10/15s vertical selfie video with native lip-synced audio of your script.

Each skill is a thin wrapper around one of agent-media's vNext primitives (`portrait_gpt2`, `character_sheet_gpt2`, `simple_selfie`, `subtitles_v2`) or the composed `make_ugc_video` pipeline. Schemas are auto-published via MCP `tools/list` — call the tool and trust the schema the server returns.

## Reference docs

- [reference/auth.md](reference/auth.md) — first-time setup
- [reference/pacing.md](reference/pacing.md) — the 2–4 words-per-second script rule
- [reference/realism-rubric.md](reference/realism-rubric.md) — realism props baked into every prompt

## How this repo is built

This repo is generated. The source of truth is the agent-media private monorepo. A GitHub Action mirrors the `public-skill/` subtree here on every push. Do not commit hand-edits — they will be overwritten.

License: Apache-2.0.
