# 🔰 Basic Ansible - Beginner Friendly Guide

Welcome to **Basic Ansible**! This folder is designed for learning Ansible from scratch with clear examples, simple playbooks, and easy-to-remember commands.

---

## 📁 Folder Structure

```
basic-ansible/
├── ansible.cfg                 # Default Ansible configuration
├── README.md                   # You are here (Complete guide)
│
├── inventory/                  # Where your managed hosts are listed
│   ├── hosts.ini               # Simple INI format
│   └── hosts.yml               # YAML format
│
├── files/                      # Static files to copy to servers
│   └── sample.txt
│
├── templates/                  # Jinja2 dynamic templates
│   └── index.html.j2
│
└── playbooks/                  # Step-by-step learning playbooks
    ├── 01-ping.yml             # Step 1: Connectivity check
    ├── 02-system-info.yml      # Step 2: Gather system facts (CPU, RAM, OS)
    ├── 03-file-management.yml  # Step 3: Create files & folders
    ├── 04-package-install.yml  # Step 4: Install software (git, curl, htop)
    ├── 05-services.yml         # Step 5: Start / stop / enable services
    ├── 06-users-groups.yml     # Step 6: Create Linux users & groups
    ├── 07-variables-and-loops.yml # Step 7: Variables, loops & conditionals
    ├── 08-handlers.yml         # Step 8: How handlers and notify work
    └── 09-simple-webserver.yml # Step 9: Mini-project (Deploy Nginx Web Server)
```

---

## 🧠 Core Ansible Concepts (In 30 Seconds)

1. **Control Node**: The machine where you run Ansible (your laptop/desktop).
2. **Managed Node / Slave**: The target servers you want to configure (Ubuntu, Amazon Linux, RedHat, etc.).
3. **Inventory**: The list of server IP addresses / hostnames (`hosts.ini`).
4. **Playbook**: A YAML file describing tasks you want Ansible to execute.
5. **Module**: Pre-built tools Ansible uses (e.g., `apt`, `yum`, `copy`, `service`, `user`).
6. **Idempotence**: Running a playbook 1 time or 100 times produces the same safe result without breaking things.

---

## ⚡ 1-Minute Quick Start (Test on Local Machine)

You can run everything on `localhost` without needing extra servers!

```bash
cd basic-ansible

# 1. Test ping
ansible-playbook playbooks/01-ping.yml -i "localhost," -c local

# 2. View system details
ansible-playbook playbooks/02-system-info.yml -i "localhost," -c local
```

---

## 🛠️ Step-by-Step Playbooks Guide

Navigate into `basic-ansible/` before running these commands:
```bash
cd /home/dc/Desktop/ansible/basic-ansible
```

### 1. Test Connection
```bash
ansible-playbook playbooks/01-ping.yml
```
> Tests if target servers are reachable and Python is working.

### 2. View System Specs (Facts)
```bash
ansible-playbook playbooks/02-system-info.yml
```
> Shows OS distribution, IP address, CPU cores, and RAM size.

### 3. Manage Files and Folders
```bash
ansible-playbook playbooks/03-file-management.yml
```
> Demonstrates `file`, `copy`, and `stat` modules.

### 4. Install Common Packages
```bash
ansible-playbook playbooks/04-package-install.yml
```
> Installs `curl`, `wget`, `git`, and `htop` cross-platform.

### 5. Control Services
```bash
ansible-playbook playbooks/05-services.yml
```
> Demonstrates starting and enabling systemd services.

### 6. Create Users and Groups
```bash
ansible-playbook playbooks/06-users-groups.yml
```
> Creates user `devuser` and assigns group `devops`.

### 7. Variables, Loops, and Conditionals
```bash
ansible-playbook playbooks/07-variables-and-loops.yml
```
> Demonstrates how to use `vars:`, `loop:`, and `when:` conditions.

### 8. Handlers and Notifications
```bash
ansible-playbook playbooks/08-handlers.yml
```
> Explains how tasks notify handlers to restart services only when changed.

### 9. Mini Project: Deploy Nginx Web Server
```bash
ansible-playbook playbooks/09-simple-webserver.yml
```
> Installs Nginx, generates a custom HTML page from a Jinja2 template, and starts the web service.

---

## 🚀 Easy Ad-Hoc Commands (Run Instantly Without Playbooks)

Run single commands directly on your servers:

```bash
# Ping all servers
ansible all -m ping

# Check server uptime
ansible all -a "uptime"

# Check free disk space
ansible all -a "df -h"

# Check memory usage
ansible all -a "free -m"

# Install a package (e.g. htop)
ansible all -m apt -a "name=htop state=present" --become

# Restart a service
ansible all -m service -a "name=nginx state=restarted" --become
```

---

## 📋 Quick Command Cheat Sheet

| Task | Command |
|---|---|
| Run playbook | `ansible-playbook playbooks/<name>.yml` |
| Dry run (Check mode) | `ansible-playbook playbooks/<name>.yml --check` |
| Run only on one group | `ansible-playbook playbooks/<name>.yml --limit webservers` |
| Run with custom inventory | `ansible-playbook playbooks/<name>.yml -i inventory/hosts.ini` |
| Ask for sudo password | `ansible-playbook playbooks/<name>.yml --ask-become-pass` |
| View detailed debug output | `ansible-playbook playbooks/<name>.yml -v` (or `-vvv`) |
| Check syntax of playbook | `ansible-playbook playbooks/<name>.yml --syntax-check` |

---

## 🎯 Next Steps

Once you are comfortable with these basics, head over to **`../advance-ansible/`** to see:
- Multi-OS dynamic roles for **Docker, Jenkins, AWS CLI, and Terraform**
- Group variables and structured inventory management
- Production master `site.yml` automation!
