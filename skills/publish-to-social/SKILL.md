---
name: 'Publish to Social'
description: 'Publish a generated agent-media video to the user''s connected TikTok, Instagram, or X. Connect channels (OAuth) and post or schedule via the REST API. Use after producing a video with make_ugc_video / make_simple_selfie.'
x-skill-slug: 'publish-to-social'
x-skill-version: '1.0.0'
---
# Publish to Social

Post a finished agent-media video (an R2 video_url from any of the video skills) to the user's connected social channels — TikTok, Instagram, or X. All calls use the user's `Authorization: Bearer ma_...` token against `https://api.agent-media.ai`.

## 1. Connect a channel (one-time, user action)

```
GET  /v1/social/providers              -> the connectable networks (tiktok, instagram, x)
POST /v1/social/connect { provider }   -> { url }   # open this OAuth url; user authorizes
GET  /v1/social/channels               -> [{ id, name, provider, profile }]  # connected channels
DELETE /v1/social/channels/:channelId  -> disconnect
```

The connect step returns an OAuth URL the **human** must open and authorize — an agent cannot complete OAuth itself. Once authorized, the channel shows up in `/v1/social/channels` with an `id`.

## 2. Publish a video

```bash
curl -X POST https://api.agent-media.ai/v1/social/publish \
  -H "Authorization: Bearer ma_..." -H "Content-Type: application/json" \
  -d '{ "video_url": "https://pub-...r2.dev/vnext/primitive-runs/<id>/...mp4",
        "channel_ids": ["<channel-id-from-/channels>"],
        "caption": "made with agent-media",
        "type": "now" }'    # or "type":"schedule" + "date":"2026-06-01T10:00:00Z"
```

`video_url` must be an agent-media R2 URL (the output of a video skill). `channel_ids` come from `/v1/social/channels`. Returns `{ success, postId }`.

## Typical flow

1. Produce a video → `make_ugc_video` (or `make_simple_selfie`) → `final_output.video_url`.
2. If the user has no connected channel, send them to connect (step 1) — you can't OAuth for them.
3. `POST /v1/social/publish` with that `video_url` + the chosen `channel_ids`.

Note: social operations run on a shared rate budget — don't poll; connect once and publish on demand.
