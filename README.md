# 🖥️ Baremetal Provisioning with Ansible

Automated bare metal server provisioning using Ansible playbooks. This project streamlines the deployment, configuration, and hardening of physical servers, reducing manual effort and ensuring consistent, repeatable infrastructure setup.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Usage](#usage)
- [Playbooks](#playbooks)
- [Variables](#variables)
- [Contributing](#contributing)
- [License](#license)

---

## 📖 Overview

This repository contains Ansible playbooks and roles for automated provisioning of bare metal servers. It covers:

- OS installation and base configuration
- Network setup (static IPs, hostname, DNS)
- Security hardening
- Package installation and service configuration
- Post-provisioning validation

---

## ✨ Features

- ⚡ Fully automated bare metal server provisioning
- 🔒 Security hardening out of the box
- 📦 Modular role-based structure
- 🔁 Idempotent playbooks — safe to run multiple times
- 🌐 Network configuration (hostname, DNS, NTP)
- 📝 Inventory-driven — manage multiple servers easily

---

## ✅ Prerequisites

### Control Node (your machine)

- Ansible `>= 2.12`
- Python `>= 3.8`
- SSH access to target servers
- SSH key pair configured

### Target Bare Metal Servers

- IPMI / iDRAC / iLO access (for PXE boot or remote management)
- Minimum: 2 vCPU, 4 GB RAM, 40 GB disk
- Supported OS: Ubuntu 20.04 / 22.04, RHEL 8 / 9, CentOS Stream 8 / 9

### Install Ansible

```bash
# Ubuntu / Debian
sudo apt update && sudo apt install ansible -y

# RHEL / CentOS
sudo dnf install ansible -y

# via pip
pip install ansible
```

---

## 📁 Project Structure

```
Baremetal-provisioning-ansible/
├── inventory/
│   ├── hosts                  # Inventory file (servers list)
│   └── group_vars/
│       ├── all.yml            # Global variables
│       └── baremetal.yml      # Baremetal-specific variables
├── roles/
│   ├── common/                # Base OS configuration
│   ├── network/               # Network setup
│   ├── hardening/             # Security hardening
│   └── packages/              # Package installation
├── playbooks/
│   ├── site.yml               # Main playbook (runs all roles)
│   ├── provision.yml          # Provisioning playbook
│   ├── network.yml            # Network configuration playbook
│   └── hardening.yml          # Security hardening playbook
├── vars/
│   └── main.yml               # Common variables
├── ansible.cfg                # Ansible configuration
└── README.md
```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/sriram10412/Baremetal-provisioning-ansible.git
cd Baremetal-provisioning-ansible
```

### 2. Configure Inventory

Edit `inventory/hosts` to add your server details:

```ini
[baremetal]
server01 ansible_host=192.168.1.10 ansible_user=root
server02 ansible_host=192.168.1.11 ansible_user=root

[baremetal:vars]
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

### 3. Set Up SSH Access

```bash
# Generate SSH key (if not already done)
ssh-keygen -t rsa -b 4096

# Copy key to target servers
ssh-copy-id root@192.168.1.10
ssh-copy-id root@192.168.1.11
```

### 4. Test Connectivity

```bash
ansible -i inventory/hosts baremetal -m ping
```

---

## ⚙️ Configuration

Update variables in `inventory/group_vars/all.yml`:

```yaml
# NTP Settings
ntp_servers:
  - 0.pool.ntp.org
  - 1.pool.ntp.org

# DNS Settings
dns_servers:
  - 8.8.8.8
  - 8.8.4.4

# Default packages to install
base_packages:
  - vim
  - curl
  - wget
  - git
  - net-tools
  - htop

# SSH hardening
ssh_port: 22
ssh_permit_root_login: "no"
ssh_password_authentication: "no"
```

---

## ▶️ Usage

### Run Full Provisioning

```bash
ansible-playbook -i inventory/hosts playbooks/site.yml
```

### Run a Specific Playbook

```bash
# Network configuration only
ansible-playbook -i inventory/hosts playbooks/network.yml

# Security hardening only
ansible-playbook -i inventory/hosts playbooks/hardening.yml
```

### Target a Specific Host

```bash
ansible-playbook -i inventory/hosts playbooks/site.yml --limit server01
```

### Dry Run (Check Mode)

```bash
ansible-playbook -i inventory/hosts playbooks/site.yml --check
```

### Use Ansible Vault for Secrets

```bash
# Encrypt sensitive variable files
ansible-vault encrypt vars/secrets.yml

# Run playbook with vault password
ansible-playbook -i inventory/hosts playbooks/site.yml --ask-vault-pass
```

---

## 📘 Playbooks

| Playbook | Description |
|---|---|
| `site.yml` | Master playbook — runs all roles in sequence |
| `provision.yml` | Base OS provisioning and initial setup |
| `network.yml` | Configures hostname, DNS, NTP, and static IPs |
| `hardening.yml` | Applies security hardening (SSH, firewall, sysctl) |

---

## 🔧 Variables

| Variable | Default | Description |
|---|---|---|
| `ansible_user` | `root` | SSH user for connecting to servers |
| `ssh_port` | `22` | SSH port |
| `ntp_servers` | `pool.ntp.org` | NTP server list |
| `dns_servers` | `8.8.8.8` | DNS resolver list |
| `base_packages` | See vars | Packages installed on all servers |
| `ssh_permit_root_login` | `no` | Allow/deny SSH root login |

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 👤 Author

**Sriram Vempati**
- GitHub: [@sriram10412](https://github.com/sriram10412)

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

> 💡 **Tip:** Always test playbooks in a staging environment before running against production servers.
