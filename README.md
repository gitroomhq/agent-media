# agent-media — Claude Skill plugin

[![npm — mcp-server](https://img.shields.io/npm/v/%40agentmedia%2Fmcp-server?label=%40agentmedia%2Fmcp-server)](https://www.npmjs.com/package/@agentmedia/mcp-server)
[![npm — CLI](https://img.shields.io/npm/v/agent-media-cli?label=agent-media-cli)](https://www.npmjs.com/package/agent-media-cli)
[![Claude plugin](https://img.shields.io/badge/claude-%2Fplugin%20install-A78BFA)](https://github.com/gitroomhq/agent-media)
[![License](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)

**Agents: read this whole page. It is everything you need to create UGC videos with agent-media — no other docs required.**

agent-media turns a short description (or a photo) + a script into a finished, captioned, lip-synced vertical UGC video. Works in Claude Code, Cursor, or any MCP / HTTP agent. One Bearer token authenticates everything.

## 1. Connect (pick one)

- **One-liner (recommended):** `npx skills add gitroomhq/agent-media` — installs all of agent-media's skills into your agent (Claude Code, Cursor, etc.).
- **Claude Code plugin (skills + MCP tools):** inside a Claude Code session run `/plugin marketplace add gitroomhq/agent-media` then `/plugin install agent-media@agent-media`.
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
- `make_product_in_hands` (v1.0.0) — Generate a 5/10/15s vertical UGC video where your character holds, wears, and shows a product. Provide a character_sheet_url (R2-hosted) and the product image (product_image_url — any https URL — OR product_image_base64; re-hosted to R2 automatically). Two modes: script for a lip-synced talking-head product review (2-4 words/sec), OR scene_action for a silent demo / b-roll. Set subject (e.g. "a young woman") to lock the person's gender/appearance so a gendered product can't drift it. framing: "close_up" (chest-up, default) or "full_body" (head-to-toe, for turn-arounds / showing the whole outfit). Both the person and the exact product are locked from the reference images.
- `make_subtitles` (v1.0.0) — Burn TikTok / Hormozi-style captions onto any vNext video (R2-hosted). Auto-transcribes via Whisper when transcript is omitted. Styles: hormozi (default), tiktok, minimal.
- `make_wireframe` (v1.0.0) — Generate a photographic storyboard / wireframe board from a character sheet (R2-hosted) + script. Multi-panel grid showing the same person performing the action progression, 4 / 6 / 8 / 10 numbered panels.
- `make_lip_sync` (v1.0.0) — Bring your own audio: lip-sync a face (an R2-hosted image / character sheet, OR an existing clip) to a provided audio track. No text-to-speech or voice cloning — the character speaks your uploaded recording. Output is a 9:16 talking-head video.
- `make_ugc_video` (v1.0.0) — End-to-end UGC video in one call. Provide EITHER a text description of the person, OR a portrait URL (R2-hosted), OR an uploaded image. The pipeline auto-generates the missing portrait, builds a character sheet, and produces a 5/10/15s vertical selfie video with native lip-synced audio of your script.
- `make_broll_talking_head` (v1.1.0) — Up-to-30s vertical talking-head video: the actor speaks full-frame while a user-supplied b-roll video is overlaid on the lower half. <=15s renders as a single take (zero cuts); 16-30s uses two takes where the voice, face and setting carry across the cut automatically (take 1's voice and final frame seed take 2) — consistent speaker and continuous scene, not a jump-cut. Provide actor_image_url (any https image) + broll_video_url (any https video — both re-hosted to R2 automatically) and EITHER script (Seedance voice) OR audio_url (your own audio, single clip <=15s). Optional: subtitles; broll_width_rate (0.1-1.0, e.g. 0.8 = b-roll 80% width centered with black margins; omit for full width); broll_start_time (seconds before the b-roll appears); broll_fade_out (dissolve the b-roll at its end).

Rules every skill follows: scripts are paced 2–4 words/sec of duration (or omit the script and pass `scene_action` for a non-speech / dancing / b-roll clip); all media URLs you pass in must be R2-hosted (the API uploads base64 images for you); each run costs credits. Reuse a character by keeping its `character_sheet_url` and feeding it to `make_simple_selfie` for each new script.

## Publish to social

Post a generated video to the user's TikTok / Instagram / X — via REST, the CLI, or MCP tools:
- `POST /v1/social/connect { provider }` → returns an OAuth `url` the user opens to authorize (agents can't OAuth for them). CLI: `agent-media social connect x`. MCP: `social_connect`.
- `GET /v1/social/channels` → the user's connected channels `[{ id, name, provider, profile }]`. CLI: `agent-media social channels`. MCP: `social_channels`.
- `POST /v1/social/publish { video_url, channel_ids, caption, type:"now"|"schedule", date? }` → re-hosts the R2 video on the network and posts/schedules it; returns `{ success, media_id, post_ids }`. CLI: `agent-media social publish`. MCP: `social_publish`.

See `skills/publish-to-social/SKILL.md` for the full flow.

## Reference docs

- [reference/auth.md](reference/auth.md) — first-time setup
- [reference/pacing.md](reference/pacing.md) — the 2–4 words-per-second script rule
- [reference/realism-rubric.md](reference/realism-rubric.md) — realism props baked into every prompt

## How this repo is built

This repo is generated. The source of truth is the agent-media private monorepo. A GitHub Action mirrors the `public-skill/` subtree here on every push. Do not commit hand-edits — they will be overwritten.

License: Apache-2.0.
