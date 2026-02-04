# codex-enable-execute-pair-macos

Re-enables the hidden **Pair Programming** + **Execute** collaboration modes in the **Codex CLI TUI** by rebuilding the upstream Rust binary with a tiny patch, then replacing the global npm vendor binary.

**macOS only.**  
**Tested with `@openai/codex@0.94.0` (Codex CLI `0.94.0`).**

## Quick start

```bash
git clone https://github.com/lueluelue2006/codex-enable-execute-pair-macos.git
cd codex-enable-execute-pair-macos
npm i -g @openai/codex@0.94.0
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
