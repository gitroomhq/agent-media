---
name: 'Agent-Media UGC Playbook'
description: 'Playbook for orchestrating an end-to-end UGC video on the agent-media vNext runtime. Read this before deciding whether to call the one-shot make_ugc_video skill or to chain the four primitives (make_portrait → make_character_sheet → make_simple_selfie → make_subtitles) manually.'
allowed-tools: ['mcp__agent-media__make_portrait', 'mcp__agent-media__make_character_sheet', 'mcp__agent-media__make_simple_selfie', 'mcp__agent-media__make_product_in_hands', 'mcp__agent-media__make_subtitles', 'mcp__agent-media__make_wireframe', 'mcp__agent-media__make_lip_sync', 'mcp__agent-media__make_ugc_video', 'mcp__agent-media__make_broll_talking_head']
x-skill-slug: 'agent-media-ugc'
x-skill-version: '1.1.0'
---
# Agent-Media UGC Playbook

You're an agent (Claude, Cursor, custom) that needs to produce a finished UGC video on the agent-media vNext runtime. This playbook gives you the three orchestration patterns and the rules of thumb for picking between them.

## Pick a pattern

| Pattern | When to use | Calls | Total time | Total credits |
| --- | --- | --- | --- | --- |
| **A — One-shot composed skill** | The user gave you a person description (or a portrait URL) AND a script. You don't need to show intermediate artifacts. | 1 (`make_ugc_video`) | ~7 min | ~185–385 |
| **B — Step-by-step primitives** | The user wants to approve each step (portrait, sheet, selfie) before moving on, OR you want different parameters at each stage. | 4 (`make_portrait` → `make_character_sheet` → `make_simple_selfie` → `make_subtitles`) | ~7–9 min | ~185–385 |
| **C — Image-first** | The user supplied a portrait image or R2 URL — skip portrait generation. | 3 (`make_character_sheet` → `make_simple_selfie` → `make_subtitles`) | ~6–8 min | ~150–350 |

## Pattern A — `make_ugc_video` (recommended default)

Single composed call. The server runs the whole pipeline inside one Temporal workflow and returns a `skill_run_id` you poll for per-step status.

```http
POST https://api.agent-media.ai/v1/skills/make_ugc_video/run
Authorization: Bearer $AGENT_MEDIA_API_KEY

{
  "description": "a friendly young woman, soft daylight, candid framing",
  "character_description": "Maya, 27 years old",
  "script": "Okay this is wild, I tried the new flow and it actually works.",
  "duration": 5,
  "subtitles": true,
  "subtitles_style": "hormozi"
}
```

Poll with `GET /v1/skills/runs/<skill_run_id>` — the response surfaces per-step artifacts (portrait_url → character_sheet_url → video_url) as each primitive completes. Final video has subtitles burned in.

See [skills/make-ugc-video/SKILL.md](../make-ugc-video/SKILL.md) for the schema.

## Step D — publish it (optional)

Once you have a `video_url`, you can post it straight to the user's TikTok / Instagram / X with the **publish-to-social** skill — `POST /v1/social/publish` (CLI `agent-media social publish`, MCP `social_publish`). The user connects each network once via OAuth (`/v1/social/connect`). See [skills/publish-to-social/SKILL.md](../publish-to-social/SKILL.md).

## Pattern B — chain the four primitives

Use when the user wants tighter control (e.g. regenerate just the portrait, or pick a different character sheet description after seeing the portrait).

```
1. POST /v1/skills/make_portrait/run            { description, realism_target }
   → wait, get portrait_url

2. POST /v1/skills/make_character_sheet/run     { portrait_url, description }
   → wait, get character_sheet_url

3. POST /v1/skills/make_simple_selfie/run       { character_sheet_url, script, duration }
   → wait, get video_url

4. POST /v1/skills/make_subtitles/run           { video_url, style: "hormozi" }
   → wait, get subtitled video_url
```

Each step's `run_id` polls via `GET /v1/primitives/runs/<run_id>`. Show the user each artifact (portrait, sheet, raw video, subtitled video) and ask for approval before moving to the next step if interactivity matters.

## Pattern C — image-first (user uploaded a portrait)

Skip step 1 of pattern B. The user's portrait must already live on R2 — either via the agent-media frontend upload, or via the `portrait_image_base64` field on `make_character_sheet` which the API uploads for you.

## Identity rules baked into every pattern

- **No "selfie" in prompt vocabulary.** Seedance treats "selfie" as "subject holds a phone with both hands". The primitive prompt builder substitutes "vertical TikTok-style close-up" / "talking-head close-up" automatically. Don't fight it.
- **Script pacing: 2–4 words per second.** 5s → 10–20 words, 10s → 20–40, 15s → 30–60. Outside this window = HTTP 400 at submit, no spend.
- **Reference image URLs must be R2-hosted.** SSRF guard rejects everything else. Use `portrait_image_base64` on `make_character_sheet` if you only have raw bytes.
- **Credits are deducted at submit.** Refunded automatically on terminal failure (insufficient credits, content-policy reject, etc.).

## Troubleshooting

- **`INSUFFICIENT_CREDITS`** — user is out. Surface the agent-media.ai billing page link.
- **`REFERENCE_URL_NOT_ALLOWED`** — you passed a non-R2 URL. Re-upload via `portrait_image_base64` on `make_character_sheet`, or to a user gallery.
- **Video has subject holding a phone** — they used a pose hint mentioning "selfie" or "phone". Strip it or set `pose: ""`.
- **Step stuck in `submitted`** — Temporal worker may be restarting. Wait 30s and re-poll; if still stuck after 5 min, the workflow is hung — contact agent-media support with the `workflow_id`.

## See also

- [reference/auth.md](../../reference/auth.md) — first-time setup
- [reference/pacing.md](../../reference/pacing.md) — 2–4 words/sec rule with examples
- [reference/realism-rubric.md](../../reference/realism-rubric.md) — the 9 realism props baked into every prompt
