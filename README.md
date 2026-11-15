# 📚 Ansible Master Repository: From Basics to Advanced Automation

This repository is organized into two structured learning and deployment sections:

1. **[`basic-ansible/`](./basic-ansible/README.md)** — Fundamentals, essential modules, step-by-step beginner playbooks, and ad-hoc commands.
2. **[`advance-ansible/`](./advance-ansible/README.md)** — Production-grade multi-OS automation using Ansible Roles for **Docker**, **Jenkins**, **AWS CLI**, and **Terraform** across **Ubuntu**, **Amazon Linux**, and **RedHat**.

---

## 📁 Repository Overview

```
ansible/
├── README.md                     ← Master guide (this file)
│
├── basic-ansible/                ← Beginner Section
│   ├── README.md                 ← Step-by-step beginner tutorial
│   ├── ansible.cfg               ← Basic configuration
│   ├── inventory/                ← INI and YAML host files
│   │   ├── hosts.ini
│   │   └── hosts.yml
│   ├── files/                    ← Static files to transfer
│   │   └── sample.txt
│   ├── templates/                ← Jinja2 dynamic templates
│   │   └── index.html.j2
│   └── playbooks/                ← 9 progressive learning playbooks
│       ├── 01-ping.yml
│       ├── 02-system-info.yml
│       ├── 03-file-management.yml
│       ├── 04-package-install.yml
│       ├── 05-services.yml
│       ├── 06-users-groups.yml
│       ├── 07-variables-and-loops.yml
│       ├── 08-handlers.yml
│       └── 09-simple-webserver.yml
│
└── advance-ansible/              ← Production & Advanced Section
    ├── README.md                 ← Advanced guide & cheat sheet
    ├── ansible.cfg               ← Production configuration
    ├── site.yml                  ← Master playbook (all tools, all nodes)
    ├── inventory/                ← Multi-OS node groups
    │   └── hosts.yml
    ├── group_vars/               ← OS-specific & global variables
    │   ├── all.yml
    │   ├── ubuntu.yml
    │   ├── amazon_linux.yml
    │   └── redhat.yml
    ├── playbooks/                ← Individual tool playbooks
    │   ├── docker.yml
    │   ├── jenkins.yml
    │   ├── awscli.yml
    │   └── terraform.yml
    └── roles/                    ← Modular roles with OS detection
        ├── docker/
        ├── jenkins/
        ├── awscli/
        └── terraform/
```

---

## ⚡ Quick Navigation

### 1️⃣ Start with Basic Ansible
Go to [`basic-ansible/`](./basic-ansible/README.md) to practice:
- Pinging and checking system specifications
- File and folder operations
- Installing packages and managing services
- Working with variables, loops, and conditions
- Deploying a sample Nginx web server

```bash
cd basic-ansible
ansible-playbook playbooks/01-ping.yml -i "localhost," -c local
```

### 2️⃣ Move to Advanced Multi-OS Deployment
Go to [`advance-ansible/`](./advance-ansible/README.md) to manage fleet infrastructure:
- Install **Docker CE + Compose** across Ubuntu, Amazon Linux, and RedHat
- Install **Jenkins LTS + Java 17**
- Install **AWS CLI v2**
- Install **Terraform**

```bash
cd advance-ansible
ansible-playbook site.yml
```

---

## 🎯 Quick Command Comparison

| Goal | Basic Ansible | Advance Ansible |
|---|---|---|
| **Directory** | `cd basic-ansible` | `cd advance-ansible` |
| **Check connectivity** | `ansible all -m ping` | `ansible slave_nodes -m ping` |
| **Run main setup** | `ansible-playbook playbooks/09-simple-webserver.yml` | `ansible-playbook site.yml` |
| **Run single tool** | `ansible-playbook playbooks/04-package-install.yml` | `ansible-playbook playbooks/docker.yml` |
| **Target specific OS** | `ansible-playbook playbooks/01-ping.yml --limit webservers` | `ansible-playbook site.yml --limit ubuntu` |
| **Dry run** | `ansible-playbook playbooks/<name>.yml --check` | `ansible-playbook site.yml --check` |

---

## 📝 Prerequisites
- **Ansible ≥ 2.12** (`pip install ansible` or `sudo apt install ansible -y`)
- **Python 3** installed on control and target machines
- **SSH access** with passwordless sudo configured on target nodes
