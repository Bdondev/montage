# Codex compatibility notes

This install is the `video-agent-skills` pack under `.agents/skills/`. The original
rendering engine, examples, scene names, and Claude/OpenAI product references in
video content are intentionally preserved.

## Codex execution policy

- Skills are discovered from `.agents/skills/`; do not relocate them to `.claude/skills/`.
- The `allowed-tools` frontmatter is retained as cross-agent metadata. Codex does
  not use Claude Code's `Bash(...)` permission syntax. In Codex, command execution
  is governed by the workspace sandbox and per-command approval.
- Do not create or update `.claude/settings.json` for Codex. The Claude permission
  recipe remains documented for Claude users; this file is the Codex-specific
  project guidance.
- Never run `npm install`, `npx`, package managers, remote install scripts,
  `codex exec`, Treg account actions, or a full render without explicit user
  approval. Version checks, `setup.mjs --check`, `npm ls`, and `node --check` are
  read-only diagnostics.

## Engine dependencies and execution assumptions

The renderer remains `.agents/skills/short-editing/engine/short.mjs`. It invokes
local child processes for ffmpeg/ffprobe, Chrome or Chromium, and whisper.cpp;
face detection uses the bundled macOS Swift helpers when available and otherwise
the MediaPipe/Chrome path. The engine dependencies are `playwright-core` and
`@mediapipe/tasks-vision`. Setup also creates runtime assets, music beds, icons,
a browser, and a speech model, so it is an installing operation even with
`--check` available.

Run checks before any render:

```bash
node -v
ffmpeg -version
ffprobe -version
yt-dlp --version
deno --version
git --version
codex --version
npm ls --prefix .agents/skills/short-editing/engine --depth=0
node .agents/skills/short-editing/engine/setup.mjs --check
```

Do not attempt a full render from this repository alone: it currently has no
`video-agent/` project, take, generated runtime, or installed engine packages.
After the user approves setup and supplies a take, the smallest smoke path is
`init` followed by `words`, `prepare`, and `stills`; run `all` only after those
pass and the stills have been inspected.

## Sub-agents

Sub-agents are optional accelerators for independent HTML motion components. The
main agent remains the only writer of `source/spec.js`, `style.json`, and the
engine, and owns `prepare`, `stills`, `render`, `audio`, and `final`. If Codex's
current harness does not expose a sub-agent facility, build components
sequentially in the main session. Never assume Claude's Agent tool, Haiku model,
or background-agent syntax exists.

## Compatibility changes made

- Replaced operational `.claude` path assumptions in setup documentation with
  `.agents`/Codex guidance while retaining a marked Claude Code path for that
  host.
- Clarified that Claude `allowed-tools` and settings files are not Codex's
  permission mechanism.
- Clarified CLI-only Treg fallback and removed the requirement to invoke a
  `claude mcp` command when operating under Codex.
- Generalized sub-agent execution wording with a sequential fallback.
- Kept example names such as `claude-5x-value`, Claude logos, Claude UI scenes,
  and example on-screen text unchanged because they are rendering fixtures, not
  host-runtime dependencies.
