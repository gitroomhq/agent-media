# agent-media

Claude Code skill for AI video and image generation via the [agent-media](https://agent-media.ai) CLI.

## What it does

This skill gives Claude Code the ability to generate AI videos and images on your behalf using the `agent-media` CLI. It supports 7 models across video and image generation:

| Model | Slug | Type | Durations |
|-------|------|------|-----------|
| Kling 3.0 Pro | `kling3` | Video | 5s, 10s |
| Veo 3.1 | `veo3` | Video | 4s, 6s, 8s |
| Sora 2 Pro | `sora2` | Video | 4s, 8s, 12s |
| Seedance 1.0 Pro | `seedance1` | Video | 5s, 10s |
| Flux 2 Pro | `flux2-pro` | Image | — |
| Flux 2 Flex | `flux2-flex` | Image | — |
| Grok Imagine | `grok-image` | Image | — |

All video models support both text-to-video and image-to-video. Duration is auto-snapped to the nearest valid value — no invalid duration errors.

## Install

### 1. Install the CLI

```bash
npm install -g agent-media-cli
```

### 2. Log in

```bash
agent-media login
```

### 3. Add the skill to Claude Code

```bash
npx add-skill gitroomhq/agent-media
```

Or manually copy `SKILL.md` into your project's `.claude/skills/agent-media/` directory.

## Usage

Once installed, ask Claude Code things like:

- "Generate a 10-second video of a robot walking through a forest using kling3"
- "Create an image of a cyberpunk cityscape at sunset with flux2-pro"
- "Take this photo and turn it into a video with seedance1" (image-to-video)
- "Show me what models are available and their pricing"
- "Check my credit balance"
- "Cancel all my running jobs"
- "Subscribe to the starter plan"

## Quick examples

```bash
# Text-to-video
agent-media generate kling3 -p "A cat on the moon" --sync

# Image-to-video
agent-media generate seedance1 -p "Make it dance" --input photo.jpg --sync

# Text-to-image
agent-media generate flux2-pro -p "Mountain landscape" --sync

# Non-interactive (for scripting)
agent-media generate sora2 -p "Ocean waves" -d 8 --no-confirm --sync --json
```

## Links

- [agent-media.ai](https://agent-media.ai) — Dashboard & account
- [agent-media-cli on npm](https://www.npmjs.com/package/agent-media-cli)
- [Documentation](https://agent-media.ai/docs)

## License

Apache-2.0
