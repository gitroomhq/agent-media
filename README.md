# agent-media — Claude Skill plugin

[![npm — mcp-server](https://img.shields.io/npm/v/%40agentmedia%2Fmcp-server?label=%40agentmedia%2Fmcp-server)](https://www.npmjs.com/package/@agentmedia/mcp-server)
[![npm — CLI](https://img.shields.io/npm/v/agent-media-cli?label=agent-media-cli)](https://www.npmjs.com/package/agent-media-cli)
[![Claude plugin](https://img.shields.io/badge/claude-%2Fplugin%20install-A78BFA)](https://github.com/gitroomhq/agent-media)
[![License](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)

**Agents: read this whole page. It is everything you need to create UGC videos with agent-media — no other docs required.**

agent-media turns a short description (or a photo) + a script into a finished, captioned, lip-synced vertical UGC video. Works in Claude Code, Cursor, or any MCP / HTTP agent. One Bearer token authenticates everything.

## 1. Connect (pick one)

- **Claude Code / Cursor:** inside a Claude Code session run `/plugin marketplace add gitroomhq/agent-media` then `/plugin install agent-media@agent-media` (installs the skills + MCP tools).
- **Any MCP agent:** run the MCP server `npx -y -p @agentmedia/mcp-server@latest agent-media-mcp` with env `AGENT_MEDIA_API_KEY=ma_...`. All skills self-describe via `tools/list`.
- **Plain HTTP:** call the REST API directly (below).

## 2. Auth

Get a Bearer token: `npm i -g agent-media-cli && agent-media login` (stores it at `~/.agent-media/credentials.json`), or grab the `ma_*` token from the dashboard. Every call uses `Authorization: Bearer ma_...`. You need credits on the account (buy at agent-media.ai).

## 3. Make a video (the one call you usually want)

`make_ugc_video` runs the whole pipeline — portrait → character sheet → lip-synced talking head → captions — in a single request.

```bash
curl -X POST https://api.agent-media.ai/v1/skills/make_ugc_video/run \
  -H "Authorization: Bearer ma_..." -H "Content-Type: application/json" \
  -d '{ "description": "a friendly 28-year-old woman, soft daylight",
        "script": "Okay, this changed my whole morning routine — you have to try it.",
        "duration": 10, "subtitles": true }'
# -> 202 { "skill_run_id": "..." }   then poll:
curl https://api.agent-media.ai/v1/skills/runs/<skill_run_id> -H "Authorization: Bearer ma_..."
# when status == "succeeded", final_output.video_url is your MP4.
```

In Claude/Cursor you just say it in words: *"Make a 10s UGC video of a friendly woman saying '…' with TikTok captions."* — the agent picks the skill.

## 4. How to call ANY skill

- **REST:** `POST https://api.agent-media.ai/v1/skills/<slug>/run` (Bearer auth, JSON body) → `202` with a `run_id` (or `skill_run_id` for `make_ugc_video`).
- **Poll:** composed skill → `GET /v1/skills/runs/<skill_run_id>`; single primitive → `GET /v1/primitives/runs/<run_id>`. Output is `final_output.video_url` / `artifacts[].url`.
- **MCP:** call the tool of the same name; arguments = the skill's input fields.
- **Exact input schema (always current):** `GET https://api.agent-media.ai/v1/public/skills` or MCP `tools/list`. Trust that over any hand-written list.

## Skills

- `make_portrait` (v1.0.0) — Generate one photoreal portrait. Optionally takes a reference photo (R2-hosted) and a realism preset. Identity is locked from the reference image when provided.
- `make_character_sheet` (v1.0.0) — Generate a magazine-style character sheet from a portrait. Provide EITHER portrait_url (must be R2-hosted) OR portrait_image_base64 (PNG/JPEG, ≤10 MB; the API will upload it to R2 first). Optional ≤10-word description for name/age/vibe hints.
- `make_simple_selfie` (v1.0.0) — Generate a 5/10/15-second vertical UGC selfie video from a character sheet. Two modes: provide a script for a lip-synced talking-head (2-4 words/sec), OR provide scene_action for a non-speech clip (dancing, b-roll, vibes) with optional background_music and no dialogue. Subject is framed waist-up, hands free, TikTok aesthetic.
- `make_subtitles` (v1.0.0) — Burn TikTok / Hormozi-style captions onto any vNext video (R2-hosted). Auto-transcribes via Whisper when transcript is omitted. Styles: hormozi (default), tiktok, minimal.
- `make_wireframe` (v1.0.0) — Generate a photographic storyboard / wireframe board from a character sheet (R2-hosted) + script. Multi-panel grid showing the same person performing the action progression, 4 / 6 / 8 / 10 numbered panels.
- `make_lip_sync` (v1.0.0) — Bring your own audio: lip-sync a face (an R2-hosted image / character sheet, OR an existing clip) to a provided audio track. No text-to-speech or voice cloning — the character speaks your uploaded recording. Output is a 9:16 talking-head video.
- `make_ugc_video` (v1.0.0) — End-to-end UGC video in one call. Provide EITHER a text description of the person, OR a portrait URL (R2-hosted), OR an uploaded image. The pipeline auto-generates the missing portrait, builds a character sheet, and produces a 5/10/15s vertical selfie video with native lip-synced audio of your script.

Rules every skill follows: scripts are paced 2–4 words/sec of duration; all media URLs you pass in must be R2-hosted (the API uploads base64 images for you); each run costs credits. Reuse a character by keeping its `character_sheet_url` and feeding it to `make_simple_selfie` for each new script.

## Reference docs

- [reference/auth.md](reference/auth.md) — first-time setup
- [reference/pacing.md](reference/pacing.md) — the 2–4 words-per-second script rule
- [reference/realism-rubric.md](reference/realism-rubric.md) — realism props baked into every prompt

## How this repo is built

This repo is generated. The source of truth is the agent-media private monorepo. A GitHub Action mirrors the `public-skill/` subtree here on every push. Do not commit hand-edits — they will be overwritten.

License: Apache-2.0.
