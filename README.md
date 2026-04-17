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
| `kali_vm.yml` | **Default.** General utils + dev tools + desktop (i3, Brave, WezTerm, VSCode, fonts) + OSCP stack |
| `playbooks/oscp.yml` | OSCP stack only (core lab tooling, OSCP-Methodology, OSCP-Automation, direnv, tmux, penelope.py, pentest-check) |

## After Install

**tmux** — tmux is installed, but dotfiles own the tmux config. The OSCP playbook does not overwrite `~/.tmux.conf`.

**OSCP workflow** — run `new-target <IP> [NAME]` to initialize a target workspace. `$IP` and `$TARGET_NAME` auto-export on `cd` via direnv.

**Optional terminal logging** — run `term-log <target>` for a shell transcript, or use `Ctrl+a P` in tmux to toggle output logging for the current pane.

**Pentest smoke test** — run `pentest-check` after provisioning to verify the main lab/exam commands are present (`nmap`, `autorecon`, `ffuf`, `netexec`, `evil-winrm`, Impacket helpers, and the local automation scripts).

## Standalone playbooks

```bash
# OSCP stack only
ansible-playbook playbooks/oscp.yml -K

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
