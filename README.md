# agent-media

Claude Code skill for AI UGC video production and media generation via the [agent-media](https://agent-media.ai) CLI.

## What it does

This skill gives Claude Code the ability to produce complete UGC videos, Product Acting UGC, and SaaS review videos on your behalf using the `agent-media` CLI.

**Flagship: UGC Pipeline** — Turn a script into a complete video with AI talking heads, B-roll cutaways, voiceover, animated subtitles, and background music. One command.

**Product Acting UGC** — Turn a product image into a creator-style product-in-hand video with an actor, scenario template, spoken script, and synced subtitles.

**SaaS Review Videos** — Provide a product name, the AI writes the script and produces a review video with an AI actor.

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

- "Create a 10-second UGC video from this script using actor sofia"
- "Make a SaaS review video for Linear with an enthusiastic angle"
- "Create a Product Acting UGC video from this perfume image"
- "Add hormozi-style subtitles to this video"
- "Create a persona from my voice sample and face photo"
- "Check my credit balance"

## Quick examples

```bash
# UGC video with AI actor
agent-media ugc "Ever wonder why some videos go viral?" --actor sofia --sync

# SaaS review video
agent-media review --saas "Linear" --actor sofia --angle enthusiastic --sync

# UGC with B-roll + product screenshots
agent-media ugc "Your script..." --actor marcus --broll --broll-images https://example.com/shot.png --sync

# Add subtitles to any video
agent-media subtitle ./video.mp4 --style hormozi --sync

# Product Acting UGC from a product image URL
agent-media product-acting \
  --product-image https://cdn.example.com/product.png \
  --actor sofia \
  --about "A premium perfume with a warm vanilla dry-down" \
  --sync
```

## Links

- [agent-media.ai](https://agent-media.ai) — Dashboard & account
- [agent-media-cli on npm](https://www.npmjs.com/package/agent-media-cli)
- [Documentation](https://agent-media.ai/docs)

## License

Apache-2.0
