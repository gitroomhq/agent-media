# agent-media-skill

Claude Code skill for AI video and image generation via the [agent-media](https://agent-media.ai) CLI.

## What it does

This skill gives Claude Code the ability to generate AI videos and images on your behalf using the `agent-media` CLI. It supports 7 models across video and image generation:

| Model | Type | Provider |
|-------|------|----------|
| Seedance 2.0 | Video | ByteDance |
| Seedance 1.5 Pro | Video | ByteDance |
| Kling 3.0 | Video | Kuaishou |
| Veo 3.1 | Video | Google DeepMind |
| Flux 2 Pro | Image | Black Forest Labs |
| Flux 2 Flex | Image | Black Forest Labs |
| Seedream 4.5 | Image | ByteDance |

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

Copy `SKILL.md` into your project's `.claude/skills/` directory, or reference this repo.

## Usage

Once installed, ask Claude Code things like:

- "Generate a video of a robot walking through a forest"
- "Create an image of a cyberpunk cityscape at sunset"
- "Show me what models are available and their pricing"
- "Check my credit balance"

## Links

- [agent-media.ai](https://agent-media.ai) — Dashboard & account
- [agent-media-cli on npm](https://www.npmjs.com/package/agent-media-cli)
- [Documentation](https://agent-media.ai/docs)

## License

Apache-2.0
