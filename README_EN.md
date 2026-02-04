# codex-enable-execute-pair-macos

⚠️ **Version requirement: only supports `@openai/codex <= 0.94.x`.**  
In `0.95+`, upstream collapses collaboration modes to `Plan/Default` and locks the hidden enum variants, so this repo’s patch does not apply (Pair/Execute cannot be exposed via the app-server properly).

Re-enables the hidden **Pair Programming** + **Execute** collaboration modes in the **Codex CLI TUI** by rebuilding the upstream Rust binary with a tiny patch, then replacing the global npm vendor binary.

Also: this patch sets the default `reasoning_effort` for all four collaboration modes (Plan / Code / Pair / Execute) to **`xhigh`**.

⚠️ On macOS 15 (Sequoia), overwriting the vendor `codex` binary in-place (e.g. with `cp`) can result in `killed` (exit=137). This script installs via “temp file → atomic `mv`”, then runs a `--version` sanity check and auto-rolls back on failure.

**macOS only.**  
**Tested with `@openai/codex@0.94.0` (Codex CLI `0.94.0`).**

## Quick start

```bash
git clone https://github.com/lueluelue2006/codex-enable-execute-pair-macos.git
cd codex-enable-execute-pair-macos
npm i -g @openai/codex@0.94.0 # 0.95+ not supported
./bin/codex-repatch-modes
```

## What this does

- Clones `openai/codex` into `~/.codex/patch-build/openai-codex` (reused on later runs)
- Checks out the tag `rust-v<version>`
- Applies `patches/openai-codex-enable-execute-pair-tui.patch`
- Builds the release binary (`cargo build --release -p codex-cli --bin codex`)
- Replaces the vendor binary inside your global npm install of `@openai/codex` (with a backup)

## Requirements (macOS)

- Node.js + npm
- Global install of Codex CLI:
  - `npm i -g @openai/codex@0.94.0`
- Git
- Rust toolchain (cargo + rustfmt)
- Xcode Command Line Tools (for the native toolchain used by Rust builds)

## Usage

From this repo root:

```bash
./bin/codex-repatch-modes
```

Optional:

```bash
./bin/codex-repatch-modes --version 0.94.0
```

## Safety notes

- This **overwrites** a file under your global npm install:
  - `$(npm root -g)/@openai/codex/vendor/<target>/codex/codex`
- A backup is created next to it (`codex.orig-<timestamp>`).
- No session data under `~/.codex/sessions` is modified.

## Disclaimer

This is an unofficial patch. Use at your own risk.

## Appendix: `code.md` vs `execute.md` (0.94.0)

These are upstream Codex templates used for **collaboration mode presets** (tag: `rust-v0.94.0`):

- `code.md`: 1 line / 26 bytes (only `you are now in code mode.`)
- `execute.md`: 45 lines / 3900 bytes (detailed “assumptions-first execution”, progress reporting, etc.)

Links:

- `code.md`: https://github.com/openai/codex/blob/rust-v0.94.0/codex-rs/core/templates/collaboration_mode/code.md
- `execute.md`: https://github.com/openai/codex/blob/rust-v0.94.0/codex-rs/core/templates/collaboration_mode/execute.md
