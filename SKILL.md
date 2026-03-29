---
name: agent-media
description: AI UGC video production from the terminal using the `agent-media` CLI.
homepage: https://github.com/gitroomhq/agent-media
metadata: {"clawdbot":{"emoji":"🌎","requires":{"bins":[],"env":[]}}}
---

npm release: https://www.npmjs.com/package/agent-media-cli
agent-media cli github: https://github.com/gitroomhq/agent-media
official website: https://agent-media.ai

# agent-media — AI UGC Video Production & Media Generation

Produce complete UGC videos and SaaS review videos from the terminal using the `agent-media` CLI.

---

## MANDATORY RULES — READ BEFORE EVERY COMMAND

**You MUST follow ALL of these rules. Violating any rule produces a broken, unwatchable video.**

### Rule 1: ALWAYS use `--actor`
Every UGC video MUST include `--actor <slug>`. Without an actor, there is no talking head and no lip sync — the video will just be a static image with voiceover. If the user hasn't specified an actor, ask them to pick one or default to a popular one like `sofia` or `naomi`. Run `agent-media actor list` to browse options.

### Rule 2: ALWAYS count words — 2.5 words per second
Natural speech is 2.5 words/second. Scripts MUST match the target duration:
- **5s video** → ~12 words max
- **10s video** → ~25 words max
- **15s video** → ~37 words max

If the user provides a script that is too long, YOU MUST rewrite it to fit. A 73-word script crammed into 15 seconds will sound robotic and lose lip sync. NEVER submit a script that exceeds the word limit.

### Rule 3: SaaS reviews MUST have screenshots
For any SaaS/product review video, you MUST provide 1-3 product screenshots via `--broll --broll-images`. Without screenshots, the video has no product context — viewers see only a talking head with no evidence of the product.

`--broll-images` accepts both **HTTP/HTTPS URLs** and **local file paths** (local files are auto-uploaded). Images are **semantically matched** to the most relevant broll scene based on filename — so use descriptive filenames! Examples:
- `--broll-images ./dashboard.png,./calendar-view.png` (local files — descriptive names!)
- `--broll-images https://example.com/pricing-page.png,https://example.com/editor.png` (URLs)
- Mix of both works too

If the user provides a product URL but no screenshots, visit the site yourself and extract image URLs from the page.

### Rule 4: SaaS reviews MUST have the product name
Ask the user: "What SaaS product should I review?" Do NOT proceed without it. The product name must appear in the script so it shows up in subtitles.

### Rule 5: Always use `--sync`
Always append `--sync` to wait for the result and get the output URL.

### Rule 6: Name screenshot files descriptively
Broll images are **semantically matched** to scenes by filename. Use descriptive names so the AI assigns the right image to the right scene:
- **GOOD**: `dashboard.png`, `calendar-view.png`, `post-editor.png`
- **BAD**: `screenshot1.png`, `IMG_0042.png`, `image.png`

When saving screenshots for `--broll-images`, rename them to describe what they show.

---

## Prerequisites

The `agent-media` CLI must be installed and authenticated:

```bash
npm install -g agent-media-cli
agent-media login
```

Verify with `agent-media whoami`. If not logged in, run `agent-media login` and follow the OTP flow.

## UGC Pipeline (Flagship Feature)

The UGC pipeline turns a script into a complete video with AI talking heads, B-roll, voiceover, and animated subtitles — one command.

### Flow

Script → Scene splitting → TTS voiceover → AI talking heads + B-roll → Crossfade assembly → Animated subtitles → Background music → End screen CTA

### Basic UGC

```bash
# ALWAYS include --actor for talking heads + lip sync
agent-media ugc "Ever wonder why some videos go viral? Here's the secret..." --actor sofia --sync

# From file
agent-media ugc ./script.txt --actor naomi --sync

# AI-generated script from a product description
agent-media ugc -g "A fitness tracker that monitors sleep quality" --actor marcus --sync
```

### UGC with B-roll

```bash
# With B-roll cutaway scenes mixed in
agent-media ugc "Your script here..." --actor marcus --broll --sync

# With product screenshots as B-roll (REQUIRED for SaaS reviews)
agent-media ugc "Your script here..." --actor sofia --broll --broll-images https://example.com/screenshot1.png,https://example.com/screenshot2.png --sync
```

### PIP Mode (Picture-in-Picture)

PIP mode creates a full-frame talking head with rotating B-roll image overlays in the lower portion. The actor speaks to camera while relevant visuals slide in and out below. Subtitles appear just above the overlay.

```bash
# Basic PIP video — actor speaks to camera with auto-generated B-roll overlays
agent-media ugc "Stop scrolling. If you struggle to grow on social media, consistency beats perfection every time." \
  --actor adaeze --pip --duration 15 --style hormozi --sync

# PIP with specific style
agent-media ugc "Three things I wish I knew before starting my business..." \
  --actor sofia --pip --duration 10 --style tiktok --sync
```

**PIP Options:**

| Flag | Values | Default | Description |
|------|--------|---------|-------------|
| `--pip-position <pos>` | `bottom-center`, `bottom-left`, `bottom-right` | `bottom-center` | B-roll overlay position |
| `--pip-size <size>` | `small`, `medium`, `large` | `medium` | B-roll overlay size (40%, 55%, 70% of width) |
| `--pip-animation <anim>` | `slide-up`, `slide-left`, `slide-right`, `fade`, `scale` | `slide-up` | Overlay entrance/exit animation |
| `--pip-style <style>` | `none`, `rounded`, `shadow` | `none` | Overlay frame style |

```bash
# PIP with bottom-right overlay, large size, slide-left animation
agent-media ugc "Your script here..." \
  --actor adaeze --pip --pip-position bottom-right --pip-size large --pip-animation slide-left --sync

# PIP with rounded overlay and scale animation
agent-media ugc "Your script here..." \
  --actor sofia --pip --pip-style rounded --pip-animation scale --duration 10 --sync
```

**PIP Rules:**
- `--actor` is **required** (PIP needs a talking head)
- Max duration is **15 seconds** (longer videos are split into ≤10s clips with seamless continuity)
- B-roll overlays are **auto-generated** from the script — no `--broll-images` needed
- Script word count rules apply: 2.5 words/sec (15s = ~37 words max)
- Do NOT combine `--pip` with `--broll` — they are separate modes

### UGC Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--actor <slug>` | Library actor for talking heads | `--actor sofia` |
| `--persona <slug>` | Custom persona (cloned voice + face) | `--persona brand-voice` |
| `--face-url <url>` | Direct face photo URL or local file | `--face-url ./photo.png` |
| `--voice <name>` | TTS voice | `--voice nova` |
| `--tone <name>` | Voice tone: energetic, calm, confident, dramatic | `--tone energetic` |
| `--style <name>` | Subtitle style (default: hormozi) | `--style bold` |
| `-d, --duration <s>` | Target duration: 5, 10, or 15 seconds | `--duration 10` |
| `--aspect <ratio>` | Aspect ratio: 9:16, 16:9, 1:1 | `--aspect 16:9` |
| `--music <genre>` | Background music: chill, energetic, corporate, dramatic, upbeat | `--music chill` |
| `--cta <text>` | End screen call-to-action text | `--cta "Follow for more"` |
| `--broll` | Enable B-roll cutaway scenes | `--broll` |
| `--broll-images <urls>` | Comma-separated screenshot/image URLs for B-roll | `--broll-images url1,url2` |
| `--pip` | PIP mode: talking head + auto-generated B-roll overlays | `--pip` |
| `--template <slug>` | Script template (see below) | `--template saas-review` |
| `-g, --generate-script <prompt>` | AI-generate script from description | `-g "yoga mat product"` |
| `--product-url <url>` | Product URL for script generation context | `--product-url https://...` |
| `-s, --sync` | Wait for completion and print output URL | `--sync` |

### Script Templates

| Template | Structure | Best For |
|----------|-----------|----------|
| `monologue` | Hook → Body → CTA | Direct-to-camera talking |
| `testimonial` | Problem → Solution → Result → CTA | Customer stories |
| `product-review` | Intro → Experience → Verdict → CTA | Product reviews |
| `problem-solution` | Hook → Pain → Solution → CTA | Before/after pain points |
| `saas-review` | Hook → Walkthrough → Opinion → CTA | SaaS/app reviews |
| `before-after` | Hook → Before → After → CTA | Transformations |
| `listicle` | Hook → Tip 1 → Tip 2 → Tip 3 + CTA | Tips and lists |
| `product-demo` | Intro → Demo → Recap → CTA | Product walkthroughs |

### Subtitle Styles

Available styles: `hormozi` (yellow karaoke highlight, default), `minimal`, `bold` (neon cyan), `karaoke` (green pop), `clean` (white on dark), `tiktok`, `neon`

## SaaS Review Videos

Generate a complete SaaS review video. **ALL FOUR are required — do NOT skip any:**
1. **Product name** in the script (so it appears in subtitles)
2. **`--actor`** (for talking head + lip sync)
3. **`--broll --broll-images`** with 1-3 product screenshot URLs
4. **Script word count** matching duration (2.5 words/sec)

### Step-by-Step Flow (FOLLOW THIS EXACTLY)

1. **Get product name** — ask user if not provided. STOP if missing.
2. **Get 1-3 screenshot URLs** — ask user, or if they give a URL, visit the site and extract `<img>` URLs showing the product dashboard/UI. STOP if no screenshots.
3. **Pick an actor** — ask user or default to `naomi` or `sofia`. Run `agent-media actor list` if they want to browse.
4. **Write the script** — MUST be ~25 words for 10s or ~37 words for 15s. Mention the product name 2-3 times. Count the words before submitting.
5. **Run the command** with ALL required flags:

```bash
# CORRECT — descriptive filenames so images match the right scenes
agent-media ugc "Postiz is the best social media tool I've used. Postiz schedules across twenty-five platforms with AI. Try Postiz today." \
  --actor naomi --duration 10 --style hormozi --broll \
  --broll-images ./postiz-dashboard.png,./postiz-calendar.png --sync

# CORRECT — URLs with descriptive paths work too
agent-media ugc "Postiz is the best social media tool I've used. Postiz schedules across twenty-five platforms with AI. Try Postiz today." \
  --actor naomi --duration 10 --style hormozi --broll \
  --broll-images "https://postiz.com/dashboard-screenshot.png,https://postiz.com/scheduling-view.png" --sync

# WRONG — no actor, no screenshots, script too long
agent-media ugc "Here's how to schedule a post in Postiz step by step..." --sync
```

### Review Flags

| Flag | Description | Example |
|------|-------------|---------|
| `--actor <slug>` | AI actor (**required**) | `--actor naomi` |
| `--broll` | Enable B-roll scenes (**required** for reviews) | `--broll` |
| `--broll-images <paths>` | Screenshot URLs or local files (**required**, 1-3) | `--broll-images ./img1.png,./img2.png` |
| `--duration <s>` | 5, 10, or 15 seconds | `--duration 10` |
| `--style <name>` | Subtitle style | `--style hormozi` |
| `--cta <text>` | End screen text | `--cta "Try it free"` |
| `-s, --sync` | Wait for result (**always use**) | `--sync` |

## Persona Management

Save voice + face combos for consistent UGC across videos:

```bash
# Create a persona from voice sample + face photo
agent-media persona create --name "brand-voice" --voice ./sample.mp3 --face ./photo.png

# List personas
agent-media persona list

# Use in UGC
agent-media ugc "Your script..." --persona brand-voice --sync

# Delete
agent-media persona delete <persona-id>
```

## Add Subtitles to Any Video

```bash
agent-media subtitle <video-path-or-job-id> --style hormozi --sync
agent-media subtitle ./my-video.mp4 --style bold --sync
```

## Pricing

| Plan | Price | Monthly Credits | ~10s Videos |
|------|-------|-----------------|-------------|
| Creator | $39/mo | 2,500 | ~3 |
| Pro | $69/mo | 5,000 | ~6 |
| Pro Plus | $129/mo | 10,000 | ~12 |

~800 credits per 10s video. Pay-as-you-go credit packs also available. Run `agent-media credits` to check balance.

## Job Management

```bash
agent-media status <job-id>     # Check job status
agent-media list                # List recent jobs
agent-media download <job-id>   # Download output media
agent-media cancel <job-id>     # Cancel and refund credits
agent-media retry <job-id>      # Retry a failed job
```

## Account

```bash
agent-media whoami              # Current user info
agent-media credits             # Credit balance
agent-media subscribe           # Manage subscription
agent-media login / logout      # Authentication
```

## Checklist Before Every UGC Command

Before running ANY `agent-media ugc` command, verify:
- [ ] `--actor` is included (no actor = no talking head = broken video)
- [ ] Script word count matches duration (count words: 5s=12, 10s=25, 15s=37)
- [ ] `--sync` is appended
- [ ] For SaaS reviews: `--broll --broll-images` with 1-3 screenshot URLs
- [ ] For SaaS reviews: product name appears 2-3 times in script
- [ ] For PIP mode: `--pip` flag is set, `--actor` is included, duration ≤ 15s
- [ ] For PIP mode: do NOT also pass `--broll` (PIP auto-generates overlays)
- [ ] Credits are sufficient (`agent-media credits`)
