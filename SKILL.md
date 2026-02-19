---
name: agent-media
description: Generate AI-powered videos and images from the terminal using the `agent-media` CLI.
homepage: https://github.com/gitroomhq/agent-media
metadata: {"clawdbot":{"emoji":"🌎","requires":{"bins":[],"env":[]}}}
---

npm release: https://www.npmjs.com/package/agent-media-cli
agent-media cli github: https://github.com/gitroomhq/agent-media
official website: https://agent-media.ai

# agent-media — AI Video & Image Generation

Generate AI-powered videos and images from the terminal using the `agent-media` CLI.

## Prerequisites

The `agent-media` CLI must be installed and authenticated:

```bash
npm install -g agent-media-cli
agent-media login
```

Verify with `agent-media whoami`. If not logged in, run `agent-media login` and follow the OTP flow.

## Available Models

| Slug | Name | Type | Durations | Max Resolution | Operations |
|------|------|------|-----------|----------------|------------|
| `kling3` | Kling 3.0 Pro | Video | 5s, 10s | 1080p | text-to-video, image-to-video |
| `veo3` | Veo 3.1 | Video | 4s, 6s, 8s | 4K | text-to-video, image-to-video |
| `sora2` | Sora 2 Pro | Video | 4s, 8s, 12s | 1080p | text-to-video, image-to-video |
| `seedance1` | Seedance 1.0 Pro | Video | 5s, 10s | 1080p | text-to-video, image-to-video |
| `flux2-pro` | Flux 2 Pro | Image | — | 2K | text-to-image |
| `flux2-flex` | Flux 2 Flex | Image | — | 2K | text-to-image |
| `grok-image` | Grok Imagine | Image | — | 1K | text-to-image |

Duration is auto-snapped to the nearest valid value for each model. If you request 6s on seedance1, it generates at 5s. If you exceed the model maximum, it clamps down automatically.

## Pricing (1 credit = $0.01)

| Model | Credits (range) |
|-------|----------------|
| kling3 | 187–374 |
| veo3 | 134–267 |
| sora2 | 200–1000 |
| seedance1 | 104–207 |
| flux2-pro | 5–8 |
| flux2-flex | 9–17 |
| grok-image | 7 |

Run `agent-media pricing <model>` for detailed per-duration/resolution breakdown.

## Plans

| Plan | Price | Monthly Credits |
|------|-------|-----------------|
| Free | $0 | 0 |
| Newby | $19/mo | 1,000 |
| Starter | $39/mo | 2,500 |
| Creator | $69/mo | 5,000 |
| Pro Plus | $119/mo | 15,000 |

Pay-as-you-go credit packs: 500 ($9), 1000 ($19), 2000 ($39), 5000 ($89).

## Core Commands

### Generate media

```bash
# Video generation
agent-media generate kling3 -p "A robot walking through a neon-lit city" --sync

# Image generation
agent-media generate flux2-pro -p "Cyberpunk samurai portrait" --sync

# Image-to-video (provide input image)
agent-media generate seedance1 -p "Make it come alive" --input ./photo.jpg --sync

# With all options
agent-media generate sora2 -p "Ocean waves at sunset" -d 8 -r 1080p --aspect-ratio 16:9 --sync

# Skip confirmation prompt
agent-media generate kling3 -p "A cat on the moon" --no-confirm --sync

# JSON output (for parsing)
agent-media generate flux2-pro -p "Mountain landscape" --no-confirm --sync --json
```

**Flags:**
- `-p, --prompt` — Generation prompt (required)
- `-d, --duration` — Video duration in seconds (auto-snapped to nearest valid)
- `-r, --resolution` — Output resolution (720p, 1080p, 2K, 4K; valid values depend on model)
- `--aspect-ratio` — Aspect ratio (16:9, 9:16, 1:1, etc.)
- `--input` — Input image path for image-to-video
- `--sync, -s` — Wait for completion, print the output URL
- `--no-confirm` — Skip the cost confirmation prompt
- `--json` — Output as JSON
- `--seed` — Reproducibility seed (integer)

### Check credits and status

```bash
agent-media credits         # Credit balance
agent-media plan            # Current subscription
agent-media status <job-id> # Job status
agent-media list            # List recent jobs
agent-media list --status completed --limit 5
```

### Model info and pricing

```bash
agent-media models              # List all models with capabilities
agent-media models --type video # Filter by type
agent-media pricing             # Overview pricing table
agent-media pricing kling3      # Detailed pricing for one model
```

### Job management

```bash
agent-media download <job-id>   # Download output media
agent-media retry <job-id>      # Retry a failed job
agent-media cancel <job-id>     # Cancel a running job (refunds credits)
agent-media cancel --all        # Cancel all active jobs
agent-media delete <job-id>     # Soft-delete a job
agent-media inspect <job-id>    # Detailed job inspection
```

### Account & billing

```bash
agent-media whoami                       # Current user info
agent-media subscribe                    # Interactive plan/credits menu
agent-media subscribe --plan starter     # Subscribe to a plan directly
agent-media subscribe --credits 500      # Buy a credit pack directly
agent-media subscribe --manage           # Open Stripe billing portal
agent-media apikey list                  # List API keys
agent-media apikey create                # Create new API key
agent-media apikey revoke <key-id>       # Revoke an API key
agent-media logout                       # Log out
```

### Diagnostics

```bash
agent-media doctor    # Run connectivity and auth diagnostics
agent-media debug     # Debug jobs and credits
agent-media update    # Check for CLI updates
```

## Tips

- `agent-media subscribe` opens Stripe Checkout in the browser then polls for up to 2 minutes until the payment is confirmed, showing the new plan/credits on success.
- Always use `--sync` when you want to wait for the result and get the output URL.
- Use `--no-confirm --sync --json` for fully non-interactive scripted usage.
- Check `agent-media credits` before generating to ensure sufficient balance.
- Duration is automatically adjusted to the nearest valid value per model — you never get a "duration not valid" error.
- Image models ignore duration — just provide a prompt and optionally resolution.
- The `--sync` flag prints the public URL of the completed media to stdout.
- Use `agent-media cancel --all` to free up concurrent job slots if you hit the limit.
