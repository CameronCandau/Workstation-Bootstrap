# Personal System Bootstrap

Installs and configures a Debian-based Linux system (Kali or Debian) to match my workstation setup.

## Quick Start

```bash
curl -sL https://raw.githubusercontent.com/Cam-Can-Do/ansible-personal/main/install.sh | sh
```

Or manually:

```bash
sudo apt-get update && sudo apt-get install -y ansible-core git
git clone https://github.com/Cam-Can-Do/ansible-personal.git
cd ansible-personal
ansible-playbook kali_vm.yml -K
```

## Playbooks

| Playbook | What it installs |
|---|---|
| `kali_vm.yml` | **Default.** General utils + dev tools + Rust toolchain + desktop (i3, Brave, WezTerm, VSCode, fonts) + OSCP stack |
| `playbooks/oscp.yml` | OSCP stack only (core lab tooling, OSCP-Methodology, OSCP-Automation, artifact-catalog, payload-server, opindex, direnv, tmux, payload folders, Penelope, pentest-check) |
| `playbooks/rust.yml` | Rustup-managed Rust toolchain for user-scoped Cargo installs |

## After Install

**tmux** — tmux is installed, but dotfiles own the tmux config. The OSCP playbook does not overwrite `~/.tmux.conf`.

**OSCP workflow** — run `new-target <IP> [NAME]` to initialize a target workspace. `$IP` and `$TARGET_NAME` auto-export on `cd` via direnv, with shell hooks added for both bash and zsh.

**Day 1** — after the playbook finishes, you should only need three commands for the usual loop:

```bash
locker-pull
payload-server linux
new-target 192.168.50.10 DC01
```

**Payload serving** — `locker-pull` populates a small curated cache under `~/tools/payloads/{linux,windows}`, then `payload-server linux` or `payload-server windows 8000 443` serves it from Kali. The wrapper uses a typo-tolerant fuzzy HTTP server by default, can start Impacket SMB with `--smb`, and can use `updog` with `--updog` when uploads/TLS/basic auth are useful.

**Artifact catalog** — `locker` is installed from crates.io and configured through `~/.config/artifact-catalog/config.yaml` to use a local user-data root at `~/.local/share/artifact-catalog` and your public ECR mirror at `public.ecr.aws/o7l3z5i2/payload-collection-metadata`. The default workflow does not require a separate catalog-content Git repo; the registry is the published source of truth.

**Command notes** — `opindex` is installed from PyPI and reads your dotfiles-managed config from `~/.config/opindex/config.yaml`.

**Shell handling** — Penelope is installed with `pipx` when available, with `~/tools/penelope.py` kept as a fallback.

**Optional terminal logging** — run `term-log <target>` for a shell transcript, or use `Ctrl+a P` in tmux to toggle output logging for the current pane.

**Pentest smoke test** — run `pentest-check` after provisioning to verify the main lab/exam commands are present (`nmap`, `autorecon`, `ffuf`, `netexec`, `evil-winrm`, `locker`, `payload-server`, `opindex`, Impacket helpers, and the local automation scripts).

## Standalone playbooks

```bash
# OSCP stack only
ansible-playbook playbooks/oscp.yml -K

# Rust toolchain only
ansible-playbook playbooks/rust.yml -K

# Single app
ansible-playbook playbooks/brave.yml -K
ansible-playbook playbooks/nvim.yml -K
```

## Configuration

All shared variables live in `group_vars/all.yml`. Override at runtime:

```bash
# Different OSCP repos location
ansible-playbook playbooks/oscp.yml -K -e oscp_dir=/opt/oscp

# Different NVIDIA driver version
ansible-playbook hardware.yml -K -e nvidia_driver_version=535
```

Inspired by: https://github.com/ThePrimeagen/dev
