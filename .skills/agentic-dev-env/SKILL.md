---
name: agentic-dev-env
description: Inspect, install, and verify a lean coding-agent runtime environment on macOS, Ubuntu, and Alibaba Cloud Linux. Use when baselining a machine or checking toolchain status.
---

# Agentic Dev Env

Last updated: 2026-03-18

## Goal

Bring machines to a lean, agent-friendly baseline:

- Keep the system package layer thin
- Standardize on `mise`, `direnv`, `uv`, and `pnpm`
- Preserve working agent CLIs
- Defer heavyweight stacks until needed

## Supported Targets

| Target OS | Package manager |
| --- | --- |
| macOS | `Homebrew` |
| Ubuntu | `apt` |
| Alibaba Cloud Linux | `dnf` |

## Baseline

| Layer | Choice |
| --- | --- |
| Runtime manager | `mise` (manages `node` and `go`) |
| Env activation | `direnv` |
| Python | `uv` |
| Node | `24.x` LTS via `mise` |
| Package manager | `pnpm` |
| Go | `1.26.x` via `mise` |
| Agent CLIs | `codex`, `claude`, `antigravity`, `opencode` |
| Core CLI | `git`, `gh`, `rg`, `fd`, `fzf`, `just`, `jq` |
| Java | Deferred |
| Containers | Deferred |

## Workflow

1. **Detect host**: `hostname`, `uname -srm`, `arch`, `$SHELL`
2. **Run status script**: `scripts/show-dev-env --json`
3. **Compare to baseline**: Identify missing tools, outdated versions, or migration needs
4. **Install/update**: Make smallest safe changes
5. **Configure shell bootstrap**: Shared PATH logic in `~/.config/shell/path.sh`
6. **Verify fresh shells**: `zsh -lic 'command -v ...'` and `bash -lic 'command -v ...'`
7. **Write status file**: `status/<machine-name>.md`

## OS-Specific Install Commands

### macOS

```sh
brew install mise direnv gh ripgrep fd fzf just jq
```

### Ubuntu

```sh
sudo apt update
sudo apt install -y curl ca-certificates git jq unzip zip xz-utils build-essential pkg-config libssl-dev
sudo apt install -y direnv gh ripgrep fzf
```

### Alibaba Cloud Linux

```sh
sudo dnf makecache
sudo dnf install -y curl ca-certificates git jq tar gzip unzip xz
sudo dnf install -y direnv ripgrep fzf
```

## Avoid

- `nvm`, `pyenv`, `pipx`, `poetry`, `yarn`, `bun`
- Manual installs under `/usr/local`

## Status File Template

```md
# Dev Env Status: <machine-name>

Updated: <timestamp>
Target: <macos | ubuntu | alibaba-cloud-linux>

## Machine Summary

| Field | Value |
| --- | --- |
| Hostname | `<hostname>` |
| OS | `<os>` |
| Arch | `<arch>` |
| Shell | `<shell>` |

## Tool Status

| Tool | Category | Version | Path | Notes |
| --- | --- | --- | --- | --- |
| `mise` | runtime | `...` | `...` | `...` |

## Actions

- ...

## Verification

- `zsh -lic 'command -v ...'` -> `...`
- `bash -lic 'command -v ...'` -> `...`

## Open Items

- ...
```

## References

- [mise getting started](https://mise.jdx.dev/getting-started.html)
- [uv installation](https://docs.astral.sh/uv/getting-started/installation/)
- [direnv setup](https://direnv.net/docs/hook.html)