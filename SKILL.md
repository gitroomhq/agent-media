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

| Slug | Name | Type | Notes |
|------|------|------|-------|
| `seedance2` | Seedance 2.0 | Video | Best motion quality, 5-10s |
| `seedance1.5` | Seedance 1.5 Pro | Video | Fast inference, 4-12s |
| `kling3.0` | Kling 3.0 | Video | Multi-shot, 5-10s |
| `veo3.1` | Veo 3.1 | Video | Google DeepMind, 8s |
| `flux2-pro` | Flux 2 Pro | Image | Professional quality |
| `flux2-flex` | Flux 2 Flex | Image | Fast, experimental |
| `seedream4.5` | Seedream 4.5 | Image | Photorealistic, up to 4K |

## Core Commands

### Generate media

```bash
# Video generation
agent-media generate seedance2 -p "A robot walking through a neon-lit city" --sync

# Image generation
agent-media generate flux2-pro -p "Cyberpunk samurai portrait" --sync

# Image-to-video (provide input image)
agent-media generate seedance2 -p "Make it dance" --input ./photo.jpg --sync

# With options
agent-media generate kling3.0 -p "Ocean waves at sunset" -d 10 -r 1080p --aspect-ratio 16:9 --sync
```

**Flags:**
- `-p, --prompt` — Generation prompt (required)
- `-d, --duration` — Video duration in seconds
- `-r, --resolution` — Output resolution (720p, 1080p)
- `--aspect-ratio` — Aspect ratio (16:9, 9:16, 1:1, etc.)
- `--input` — Input image for image-to-video
- `--sync, -s` — Wait for completion and print the output URL
- `--json` — Output as JSON (for parsing)

### Check credits and status

```bash
# Credit balance
agent-media credits

# Current plan
agent-media plan

# Job status
agent-media status <job-id>

# List recent jobs
agent-media list
agent-media list --status completed --limit 5
```

### Model info and pricing

```bash
# List all models
agent-media models

# Detailed pricing
agent-media pricing
agent-media pricing --model seedance2
```

### Job management

```bash
# Download a completed job
agent-media download <job-id>

# Retry a failed job
agent-media retry <job-id>

# Cancel a running job
agent-media cancel <job-id>

# Delete a job
agent-media delete <job-id>
```

### Account

```bash
agent-media whoami          # Current user
agent-media credits         # Credit balance
agent-media plan            # Current subscription
agent-media subscribe       # Manage subscription
agent-media apikey list     # List API keys
agent-media apikey create   # Create new API key
```

## Tips

- Always use `--sync` when you want to wait for the result and get the output URL.
- Use `--json` when you need to parse the output programmatically.
- Check `agent-media credits` before generating to ensure sufficient balance.
- For video, default duration is 5s and default resolution is 720p if not specified.
- Image models don't need duration — just prompt and optionally resolution.
- The `--sync` flag prints the public URL of the completed media.
