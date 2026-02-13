# Ansible Workstation Setup

Automated setup for my workstations using Ansible.

## Structure

```
ansible-workstation/
├── README.md
├── inventory.ini              # Remote machines (desktop/laptop)
├── localhost-inventory.ini    # For running on localhost (Mac)
├── macbook.yml               # MacBook playbook
├── cachyos.yml               # CachyOS playbook (desktop/laptop)
└── vars/
    ├── mac-packages.yml      # Mac-specific packages
    └── cachyos-packages.yml  # CachyOS-specific packages
```

## Usage

### MacBook Setup (localhost)

Run on the MacBook itself:

```bash
ansible-playbook -i localhost-inventory.ini macbook.yml
```

### CachyOS Setup (remote)

Run from MacBook to configure desktop/laptop:

```bash
# Both machines
ansible-playbook -i inventory.ini cachyos.yml --ask-become-pass

# Specific machine
ansible-playbook -i inventory.ini cachyos.yml --limit desktop --ask-become-pass
ansible-playbook -i inventory.ini cachyos.yml --limit laptop --ask-become-pass
```

## Prerequisites

### On MacBook (Control Node)

1. Install Ansible:
   ```bash
   brew install ansible
   ```

2. Install required collections:
   ```bash
   ansible-galaxy collection install community.general
   ansible-galaxy collection install kewlfft.aur
   ```

3. GitHub SSH key added to account

### On CachyOS Machines (Target)

1. Fresh CachyOS installation
2. Username: `euchey` (must be consistent)
3. SSH server running:
   ```bash
   sudo systemctl enable --now sshd
   ```
4. Static IP configured in pfSense
5. SSH key from MacBook copied:
   ```bash
   ssh-copy-id euchey@192.168.1.50  # from MacBook
   ```

## Reimage Workflow

1. **Install CachyOS** on target machine (username: `euchey`)
2. **Enable SSH**: `sudo systemctl enable --now sshd`
3. **From MacBook**: Copy SSH key (if needed): `ssh-copy-id euchey@192.168.1.50`
4. **From MacBook**: Run playbook: `ansible-playbook -i inventory.ini cachyos.yml --limit desktop --ask-become-pass`
5. **Wait ~30 minutes** while Ansible configures everything
6. **Done!**

## Configuration

- **inventory.ini**: Update with your machine IPs
- **vars/mac-packages.yml**: Customize Mac packages
- **vars/cachyos-packages.yml**: Customize Linux packages

## What Gets Installed

### MacBook
- Git, Neovim, Node.js
- Claude Code CLI
- Ghostty terminal
- LazyVim config (symlinked from unix_dotfiles)

### CachyOS
- Development tools (git, neovim, docker, postgresql, etc.)
- Modern CLI tools (eza, bat, ripgrep, zoxide, etc.)
- Desktop apps (browsers, terminals, etc.)
- LazyVim config (symlinked from unix_dotfiles)
- And much more...
