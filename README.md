# codex-enable-execute-pair-macos

注意前置条件（缺了会直接报错）：`git`、Rust（`cargo`/`rustfmt`）、Xcode Command Line Tools、Node + `npm`。首次运行会比较久，因为需要下载 Rust 依赖。成功标志是脚本最后输出：`ok: installed patched codex binary`。

English version: [README_EN.md](README_EN.md)

通过对上游 Rust 二进制打一个很小的补丁并重新编译，然后替换全局 npm 安装里的 vendor 二进制，让 **Codex CLI 的 TUI** 恢复显示并可切换隐藏的 **Pair Programming** + **Execute** 协作模式。

另外：本补丁会把上游 **Execute** 模式的默认 `reasoning_effort` 从 `high` 调整为 `medium`（避免默认高推理）。

**仅支持 macOS。**  
**已在 `@openai/codex@0.94.0`（Codex CLI `0.94.0`）验证可用。**

## 快速开始

```bash
git clone https://github.com/lueluelue2006/codex-enable-execute-pair-macos.git
cd codex-enable-execute-pair-macos
npm i -g @openai/codex@0.94.0
./bin/codex-repatch-modes
```

## 它做了什么

- 把 `openai/codex` 克隆到 `~/.codex/patch-build/openai-codex`（后续复用）
- 切到 tag：`rust-v<version>`
- 应用补丁：`patches/openai-codex-enable-execute-pair-tui.patch`
- 编译 release 二进制：`cargo build --release -p codex-cli --bin codex`
- 替换全局 npm 安装的 `@openai/codex` vendor 二进制（会先备份）

## 依赖（macOS）

- Node.js + `npm`
- 全局安装 Codex CLI：
  - `npm i -g @openai/codex@0.94.0`
- `git`
- Rust 工具链（`cargo` + `rustfmt`）
- Xcode Command Line Tools（Rust 编译会用到本机工具链）

## 用法

在本仓库根目录执行：

```bash
./bin/codex-repatch-modes
```

可选（手动指定版本）：

```bash
./bin/codex-repatch-modes --version 0.94.0
```

## 安全说明

- 它会**覆盖**你全局 npm 安装目录下的一个文件：
  - `$(npm root -g)/@openai/codex/vendor/<target>/codex/codex`
- 会在同目录自动生成备份：`codex.orig-<timestamp>`
- 不会修改 `~/.codex/sessions` 下的历史会话数据

## 免责声明

这是非官方补丁，风险自负。

## 附录：`code.md` vs `execute.md`（0.94.0）

这是 Codex 上游用于 **collaboration mode presets** 的模板文件（tag：`rust-v0.94.0`）：

- `code.md`：1 行 / 26 bytes（只写 `you are now in code mode.`）
- `execute.md`：45 行 / 3900 bytes（详细说明 “Assumptions-first execution / Reporting progress / Executing”等）

链接：

- `code.md`：https://github.com/openai/codex/blob/rust-v0.94.0/codex-rs/core/templates/collaboration_mode/code.md
- `execute.md`：https://github.com/openai/codex/blob/rust-v0.94.0/codex-rs/core/templates/collaboration_mode/execute.md
