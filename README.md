# Raspberry Pi homelab

Ansible playbooks and roles for automating my homelab setup on a Raspberry Pi 5
running Ubuntu Server 24.04 LTS (ARM64).

This repo keeps my configuration in one place so I can easily set up or rebuild
my Raspberry Pi.

## What it configures

- Basic system setup: packages (including Go), timezone, SSH, and `/opt/homelab`.
- Docker Engine, Buildx, and Docker Compose from Docker's APT repository.
- Docker group membership for the SSH user.

## Project structure

```text
.
├── ansible.cfg                 # Ansible settings
├── requirements.yml            # Required Ansible collections
├── site.yml                    # Main playbook: common, then Docker
├── inventory/
│   └── hosts.ini               # Raspberry Pi address and SSH user
├── group_vars/
│   └── raspberrypi.yml         # Packages, timezone, and shared settings
├── playbooks/                  # One-off playbooks
└── roles/
    ├── common/                # Basic system configuration
    └── docker/                # Docker installation and configuration
```

## Usage

Install Ansible on your control machine. The Pi needs Ubuntu, Python 3, SSH,
and an existing user with sudo access.

Update `inventory/hosts.ini` with your Pi's address and SSH user, then adjust
`group_vars/raspberrypi.yml` for your setup.

Run these commands from the repository root:

```bash
# Install required collections
ansible-galaxy collection install -r requirements.yml

# Validate the playbook
ansible-playbook site.yml --syntax-check

# Configure the Pi
ansible-playbook site.yml --ask-become-pass
```

Enter the Pi user's sudo password at the prompt. Omit `--ask-become-pass` if
passwordless sudo is configured. Add `--step` to confirm each task before it runs.

After Docker installation, reconnect your SSH session or run `newgrp docker`
to activate your Docker group membership. Then test it on the Pi:

```bash
docker run hello-world
```

## One-off playbooks

Keep standalone playbooks in `playbooks/` and run them from the repository root:

```bash
ansible-playbook playbooks/<name>.yml --ask-become-pass
```

Role task files under `roles/<name>/tasks/main.yml` contain task lists. Full
playbooks define `hosts`, `become`, and the tasks or roles to run.
