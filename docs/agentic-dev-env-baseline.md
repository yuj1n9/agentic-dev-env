# Agent-Friendly Development Environment Baseline

Last updated: 2026-03-12

## Plan-of-Record

This Mac will use a lightweight, repeatable baseline for coding agents:

- `Homebrew` for system packages
- `mise` for runtime version management
- `direnv` for repo-local environment activation
- `uv` for Python runtime and dependency workflows
- `pnpm` for Node/TypeScript package management
- `mise`-managed Node and Go instead of shell-coupled or manual installs
- `Java` and `Container` intentionally deferred as `Future` to save disk space

## Baseline Matrix

| Layer | Choice | Status | Repo / Config Artifact | Notes |
| --- | --- | --- | --- | --- |
| System packages | `Homebrew` | Installed | `Brewfile` optional | Keep OS-level installs small and explicit |
| Shell bootstrap | Shared shell bootstrap sourced by both `~/.zprofile` and `~/.zshrc` | Configured | `~/.config/shell/*` | Prevent PATH drift between agents and interactive shells |
| Runtime manager | `mise` | Installed + configured | `~/.config/mise/config.toml`, `mise.toml` | One manager for Node and Go today; can grow later |
| Project env activation | `direnv` | Installed | `.envrc` | Auto-load repo env vars and local paths |
| Python runtime + deps | `uv` | Kept | `pyproject.toml`, `uv.lock` | Use `uv` for venvs, installs, sync, and tool execution |
| Node runtime | Node `24.x` LTS via `mise` | Configured (`24.14.0`) | `~/.config/mise/config.toml`, `mise.toml` | Replaces `nvm` as the default in fresh shells |
| Node package manager | `pnpm` | Installed (`10.32.1`) | `package.json`, `pnpm-lock.yaml` | Pin with `"packageManager"` per repo |
| Go runtime | Go `1.26.x` via `mise` | Configured (`1.26.1`) | `~/.config/mise/config.toml`, `mise.toml` | Replaces manual `/usr/local/go` as the default in fresh shells |
| Go dependencies | `go mod` / `go work` | Ready | `go.mod`, `go.sum`, `go.work` optional | Stay with native tooling |
| Rust | `rustup` + `cargo` | Later, if needed | `Cargo.toml`, `Cargo.lock` | Do not install preemptively |
| Java | Temurin JDK + Gradle/Maven wrapper | Future | `gradlew` or `mvnw` | Intentionally deferred |
| Container | `OrbStack` | Future | `.devcontainer/`, `compose.yaml` if needed | Intentionally deferred |
| Agent CLIs | Native installs for Codex, Claude Code, Antigravity | Kept | Agent-specific paths only | Preserve working installs |
| Core CLI tools | `gh`, `ripgrep`, `fd`, `fzf`, `just` | Installed | `Brewfile` optional | Small, high-value tools for humans and agents |

## Current Machine State

### Status script

Use [`show-dev-env`](/Users/jing/Code/workspace/agentic-dev-env/scripts/show-dev-env) to report the current toolchain state.

- Default: human-friendly table
- `--json`: machine-readable output for agents and automation

Examples:

```sh
scripts/show-dev-env
scripts/show-dev-env --json | jq
```

Use [`show-dev-env-ubuntu`](/Users/jing/Code/workspace/agentic-dev-env/scripts/show-dev-env-ubuntu) for the Ubuntu-targeted baseline report.

- Includes Ubuntu-first recommendations (`apt`, optional `nala`, `podman` preference)
- Works on non-Ubuntu hosts and marks host/target mismatch clearly
- Supports the same default human output plus `--json`

Examples:

```sh
scripts/show-dev-env-ubuntu
scripts/show-dev-env-ubuntu --json | jq
```

### Installed / configured on this Mac

| Tool | Version | Resolution path |
| --- | --- | --- |
| `mise` | `2026.3.8` | `/opt/homebrew/bin/mise` |
| `direnv` | `2.37.1` | `/opt/homebrew/bin/direnv` |
| `uv` | `0.10.9` | `/Users/jing/.local/bin/uv` |
| `node` | `v24.14.0` | `/Users/jing/.local/share/mise/shims/node` |
| `npm` | `11.9.0` | `/Users/jing/.local/share/mise/shims/npm` |
| `pnpm` | `10.32.1` | `/opt/homebrew/bin/pnpm` |
| `go` | `go1.26.1` | `/Users/jing/.local/share/mise/shims/go` |
| `gh` | `2.88.0` | `/opt/homebrew/bin/gh` |
| `ripgrep` | `15.1.0` | `/opt/homebrew/bin/rg` |
| `fd` | `10.4.2` | `/opt/homebrew/bin/fd` |
| `fzf` | `0.70.0` | `/opt/homebrew/bin/fzf` |
| `just` | `1.46.0` | `/opt/homebrew/bin/just` |
| `claude` | `2.1.74` | `/Users/jing/.local/bin/claude` |
| `antigravity` | `1.107.0` | `/Users/jing/.antigravity/antigravity/bin/antigravity` |
| `codex` | `0.115.0-alpha.4` | `/Applications/Codex.app/Contents/Resources/codex` |

### Shell files updated

- `~/.config/shell/path.sh`
- `~/.config/shell/zsh-interactive.zsh`
- `~/.zprofile`
- `~/.zshrc`
- `~/.bash_profile`
- `~/.bashrc`
- `~/.profile`

### Global `mise` config

`~/.config/mise/config.toml`

```toml
[tools]
go = "1.26"
node = "24"
```

## Notes From This Setup

- Fresh `zsh` and `bash` shells now resolve `node` and `go` from `mise`.
- `uv` remains the Python tool owner and was not replaced.
- Existing agent CLIs were preserved and still resolve from their current install locations.
- Legacy `nvm` has been removed from disk.
- Fresh shells now strip legacy `nvm` and `/usr/local/go/bin` PATH entries before activating the managed toolchain.
- The old manual `/usr/local/go` directory still exists on disk because it is root-owned and requires an admin-password removal outside this session.
- `Java` and `Container` remain intentionally deferred as `Future`.
- `Rust` was not installed because there is no active repo need yet.
- `mise` warned that `gpg` is not installed, so artifact signature verification was skipped during tool install. Treat `gnupg` as an optional later hardening step.

## Install-Now Set

- `mise`
- `direnv`
- `pnpm`
- `gh`
- `ripgrep`
- `fd`
- `fzf`
- `just`

## Keep-As-Is

- `Homebrew`
- `uv`
- `git`
- `Codex`
- `Claude Code`
- `Antigravity`

## Defer

- `Java`
- `Container`
- Rust, unless a current repo needs it

## Avoid Adding By Default

- `nvm` for new work
- `pyenv`
- `pipx`
- `poetry`
- `yarn`
- `bun`
- Manual language installs under `/usr/local` unless a toolchain forces it

## Per-Repo Baseline

Every active repo should aim to carry:

- `mise.toml` for runtime versions
- `.envrc` for repo-local environment loading
- Language-native lockfiles
- A small `README.md` with setup, test, and common commands
- `AGENTS.md` or equivalent agent instructions when the repo benefits from agent context

## Versioning Rules

- Pin major/minor runtime versions in `mise.toml`
- Pin package managers where supported
- Commit lockfiles
- Prefer project-local commands over globally installed CLIs

## Shell Rules

- Put shared PATH/bootstrap logic in one place and source it from `~/.zprofile` and `~/.zshrc`
- Keep interactive shell cosmetics separate from toolchain setup
- Avoid relying on shell-manager side effects that only exist in one shell mode

## Migration Direction On This Mac

1. Keep the current agents
2. Add `mise` and move Node to it first
3. Add `pnpm` and start pinning it per repo
4. Move Go from manual install to `mise`
5. Add `direnv` after shell bootstrap is cleaned up
6. Leave `Java` and `Container` untouched until there is a concrete need

Status on 2026-03-12: steps 1 through 5 are complete for this machine baseline.

## Setup Log

Completed on 2026-03-12:

- Installed `mise`, `direnv`, `pnpm`, `gh`, `ripgrep`, `fd`, `fzf`, and `just` with `Homebrew`
- Created a shared shell bootstrap under `~/.config/shell/`
- Switched fresh-shell default `node` to `mise` (`24.14.0`)
- Switched fresh-shell default `go` to `mise` (`1.26.1`)
- Left `uv` in place for Python workflows
- Kept `Codex`, `Claude Code`, and `Antigravity` unchanged
- Verified command resolution in fresh `zsh` and `bash` shells
- Removed the unused `~/.nvm` directory
- Updated shell bootstrap to strip legacy `nvm` and `/usr/local/go/bin` PATH entries

## Verification

Fresh-shell verification completed on 2026-03-12:

- `node` -> `/Users/jing/.local/share/mise/shims/node`
- `go` -> `/Users/jing/.local/share/mise/shims/go`
- `pnpm` -> `/opt/homebrew/bin/pnpm`
- `uv` -> `/Users/jing/.local/bin/uv`
- `rg` -> `/opt/homebrew/bin/rg`
- `fd` -> `/opt/homebrew/bin/fd`
- `claude` -> `/Users/jing/.local/bin/claude`
- `antigravity` -> `/Users/jing/.antigravity/antigravity/bin/antigravity`

Legacy cleanup verification completed on 2026-03-12:

- `~/.nvm` removed, reclaiming about `221M`
- `/usr/local/go` still present on disk, about `258M`, but no longer used by fresh shells

## Next Repo-Level Step

This workspace is just the baseline record, so no project-local `mise.toml` or `.envrc` was created here. For each real repo, add:

- `mise.toml`
- `.envrc`
- lockfiles
- a short `README.md`
- `AGENTS.md` when the repo benefits from agent guidance
