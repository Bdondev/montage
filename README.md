# Montage

Montage is a local, agent-assisted video production workspace for creating short-form and long-form videos. It combines creator workflows with a deterministic paper-stage rendering engine for captions, camera layouts, motion graphics, audio, and final video output.

The repository is configured for OpenAI Codex while preserving the original video rendering engine, examples, and cross-agent skill metadata.

## What is included

- Creator onboarding for voice, style, tools, and publishing preferences
- Script planning, hook development, and transcript-aware editing workflows
- Talking-head shorts with paper boards, captions, camera layouts, and sound
- Long-video repurposing into finished short clips
- B-roll indexing and placement
- Style analysis and reusable editing styles
- Optional image generation through Codex
- Optional Instagram, TikTok, and YouTube workflows through Treg
- A local Node.js rendering engine with real project examples

Available skills are documented under `.agents/skills/`, including:

`studio-setup`, `studio-script`, `short-editing`, `clips`, `broll`, `steal`, `short-translate`, `codex-images`, `vox`, `insta-audit`, `outliers`, and `publish`.

## Requirements

The supported local workflow requires:

- Node.js 20 or newer
- FFmpeg and ffprobe
- yt-dlp
- Deno, for yt-dlp's YouTube JavaScript runtime needs
- Chrome, Chromium, Edge, or Brave
- macOS Homebrew on macOS when whisper.cpp is not already installed

The engine uses `playwright-core` and `@mediapipe/tasks-vision`. The setup script installs those project-local npm dependencies, installs whisper.cpp when needed, downloads the local Whisper model, and generates the bundled music and icon assets.

## Installation

Clone the repository and enter the project:

```bash
git clone https://github.com/Bdondev/montage.git
cd montage
```

Verify the command-line tools:

```bash
node -v
ffmpeg -version
ffprobe -version
yt-dlp --version
deno --version
```

Initialize the rendering engine:

```bash
node .agents/skills/short-editing/engine/setup.mjs
```

Confirm readiness:

```bash
node .agents/skills/short-editing/engine/setup.mjs --check
npm ls --prefix .agents/skills/short-editing/engine --depth=0
node .agents/skills/short-editing/engine/whisper.mjs --check
```

The setup is intentionally separate from cloning. Git excludes generated dependencies, runtime caches, secrets, raw takes, and rendered media so a new machine can install its local runtime without bloating the repository.

## Creator workspace

The creator-specific workspace lives in `video-agent/`:

```text
video-agent/
├── voice.md                 creator voice and profile
├── style.json               default style selector
├── styles/default/          default visual style and logo assets
├── scripts/                 weekly scripts
├── takes/                   raw recordings, ignored by Git
├── edits/                   edit projects and rendered outputs
├── repurpose/               long-video clip plans
├── images/                  generated stills and covers
├── audits/                  account audit reports
├── swipe.md                 hook and reference library
├── log.md                   script log
├── .tmp/                    temporary working files, ignored by Git
└── .env                     local secrets, ignored by Git
```

The initial profile is intentionally a placeholder. Fill it with your own language, audience, experience, real samples, claims, boundaries, and calls to action before relying on automated script generation.

## First edit

Place a source recording in `video-agent/takes/`, then initialize an edit:

```bash
node .agents/skills/short-editing/engine/short.mjs init \
  video-agent/edits/short-my-video \
  --source video-agent/takes/my-video.mp4
```

Generate word timings, prepare the timeline, and inspect stills before a full render:

```bash
node .agents/skills/short-editing/engine/short.mjs words \
  video-agent/edits/short-my-video

node .agents/skills/short-editing/engine/short.mjs prepare \
  video-agent/edits/short-my-video

node .agents/skills/short-editing/engine/short.mjs stills \
  video-agent/edits/short-my-video 0.3 2.9 6.4
```

After reviewing the generated stills, render the finished video:

```bash
node .agents/skills/short-editing/engine/short.mjs all \
  video-agent/edits/short-my-video
```

The engine's full workflow and scene library are documented in `.agents/skills/short-editing/SKILL.md` and its `references/` directory.

## Customizing the visual style

The default look is a textured paper stage with a full-width camera, mono capital captions, and a black caption band across the seam. Customize `video-agent/styles/default/style.json`, then keep the selector in `video-agent/style.json` pointed at `default`.

The available starting looks are defined in `.agents/skills/short-editing/assets/looks.json`:

- `default` — minimal paper and mono-band captions
- `classique` — sticker captions
- `papier` — framed camera, deep paper shadows, and rise captions
- `pop` — yellow accent and hard-shadow captions
- `poster` — orange paper, hard cuts, and condensed display captions

Use the style schema in `.agents/skills/short-editing/references/style-schema.md` when adding colors, fonts, captions, pacing, music, or creator CTA details.

## Codex compatibility

Codex discovers the skills from `.agents/skills/`. `AGENTS.md` documents the Codex execution policy, approval boundaries, dependency assumptions, and the fallback from parallel sub-agents to sequential work when a sub-agent facility is unavailable.

The `allowed-tools` metadata in individual skills is retained for compatibility with other agent hosts. Codex command execution is governed by the workspace sandbox and explicit command approvals; this repository does not require `.claude/settings.json`.

## Privacy and credentials

Most rendering is local. Optional integrations can access external services or incur usage charges, including Treg, ElevenLabs, and Codex image generation.

Never commit:

- `video-agent/.env` or other environment files
- API keys, login tokens, or private account data
- raw recordings and personal media
- rendered videos or temporary assets
- `node_modules/` or local runtime directories

The repository `.gitignore` covers these categories. Always inspect staged files before committing:

```bash
git diff --cached --name-only
```

## Examples

Real engine examples are in `.agents/skills/short-editing/examples/`. Each example demonstrates a complete specification, transcript timing, or HTML motion component. Start with `examples/skills-montage/` for the smallest complete short, then review the contact sheets and the more advanced UI examples.

## Repository status

This is a creator-focused public workspace built around the upstream [`video-agent-skills`](https://github.com/Melvynx/video-agent-skills) project. The installed skill sources and versions are recorded in `skills-lock.json`.

No license file is currently included. Add an appropriate license before accepting external contributions or redistributing the repository.
