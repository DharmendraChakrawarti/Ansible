# 🚀 Ansible - Install DevOps Tools on Multiple Servers

Install **Docker**, **Jenkins**, **AWS CLI**, and **Terraform** on **Ubuntu**, **Amazon Linux**, and **RedHat** servers — all at once using Ansible.

---

## 📁 Project Structure

```
ansible/
├── ansible.cfg              ← Ansible settings
├── site.yml                 ← Main playbook (installs ALL tools)
├── README.md                ← This file
│
├── inventory/
│   └── hosts.yml            ← Your server IPs go here
│
├── group_vars/
│   ├── all.yml              ← Common settings (SSH user, versions)
│   ├── ubuntu.yml           ← Ubuntu settings
│   ├── amazon_linux.yml     ← Amazon Linux settings
│   └── redhat.yml           ← RedHat settings
│
├── playbooks/               ← Run one tool at a time
│   ├── docker.yml
│   ├── jenkins.yml
│   ├── awscli.yml
│   └── terraform.yml
│
└── roles/                   ← Actual install logic
    ├── docker/              ← Docker CE + Compose
    ├── jenkins/             ← Jenkins + Java 17
    ├── awscli/              ← AWS CLI v2
    └── terraform/           ← Terraform
```

---

## 🔧 STEP 1 — Setup (One-Time Only)

### 1.1 Install Ansible on your machine

```bash
# Ubuntu / Debian
sudo apt update && sudo apt install ansible -y

# Amazon Linux / RedHat
sudo yum install ansible -y

# Using pip (any OS)
pip install ansible
```

### 1.2 Generate SSH key (if you don't have one)

```bash
ssh-keygen
```
> Just press Enter for all prompts.

### 1.3 Copy SSH key to each server

```bash
ssh-copy-id user@SERVER_IP
```

**Example:**
```bash
ssh-copy-id ubuntu@192.168.1.10
ssh-copy-id ec2-user@192.168.1.20
ssh-copy-id ec2-user@192.168.1.30
```

### 1.4 Allow passwordless sudo on each server

SSH into each server and run:
```bash
echo "$USER ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/$USER
```

---

## 🔧 STEP 2 — Add Your Server IPs

Edit `inventory/hosts.yml` and put your real IPs:

```bash
nano inventory/hosts.yml
```

Change the IPs:
```yaml
ubuntu:
  hosts:
    ubuntu-node-1:
      ansible_host: 192.168.1.10    # ← your Ubuntu server IP

amazon_linux:
  hosts:
    amazon-node-1:
      ansible_host: 192.168.1.20    # ← your Amazon Linux IP

redhat:
  hosts:
    redhat-node-1:
      ansible_host: 192.168.1.30    # ← your RedHat server IP
```

---

## 🔧 STEP 3 — Update SSH User (if needed)

Edit `group_vars/all.yml`:

```bash
nano group_vars/all.yml
```

Change `ansible_user` to match your server login:
```yaml
ansible_user: ubuntu          # or ec2-user, root, etc.
```

---

## ✅ STEP 4 — Test Connection

```bash
ansible all -m ping
```

**Expected output:**
```
ubuntu-node-1 | SUCCESS
amazon-node-1 | SUCCESS
redhat-node-1 | SUCCESS
```

---

## 🚀 STEP 5 — Install Tools

### Install EVERYTHING (Docker + Jenkins + AWS CLI + Terraform)

```bash
ansible-playbook site.yml
```

### Install ONE tool at a time

```bash
ansible-playbook playbooks/docker.yml       # Only Docker
ansible-playbook playbooks/jenkins.yml      # Only Jenkins
ansible-playbook playbooks/awscli.yml       # Only AWS CLI
ansible-playbook playbooks/terraform.yml    # Only Terraform
```

### Install on ONE OS group only

```bash
ansible-playbook site.yml --limit ubuntu          # Only Ubuntu servers
ansible-playbook site.yml --limit amazon_linux    # Only Amazon Linux servers
ansible-playbook site.yml --limit redhat          # Only RedHat servers
```

### Install on ONE specific server

```bash
ansible-playbook site.yml --limit ubuntu-node-1
```

### Dry run (see what will happen without doing it)

```bash
ansible-playbook site.yml --check
```

---

## 📋 Cheat Sheet — All Commands in One Place

| What you want to do | Command |
|---|---|
| **Test connection** | `ansible all -m ping` |
| **Install everything** | `ansible-playbook site.yml` |
| **Install Docker only** | `ansible-playbook playbooks/docker.yml` |
| **Install Jenkins only** | `ansible-playbook playbooks/jenkins.yml` |
| **Install AWS CLI only** | `ansible-playbook playbooks/awscli.yml` |
| **Install Terraform only** | `ansible-playbook playbooks/terraform.yml` |
| **Target Ubuntu only** | `ansible-playbook site.yml --limit ubuntu` |
| **Target Amazon only** | `ansible-playbook site.yml --limit amazon_linux` |
| **Target RedHat only** | `ansible-playbook site.yml --limit redhat` |
| **Target one server** | `ansible-playbook site.yml --limit ubuntu-node-1` |
| **Dry run** | `ansible-playbook site.yml --check` |
| **Verbose mode** | `ansible-playbook site.yml -v` |
| **Super verbose** | `ansible-playbook site.yml -vvv` |

---

## ✅ STEP 6 — Verify Installation

Run these to check versions on all servers:

```bash
ansible all -m shell -a "docker --version"
ansible all -m shell -a "jenkins --version"
ansible all -m shell -a "aws --version"
ansible all -m shell -a "terraform version"
```

Check service status:

```bash
ansible all -m shell -a "systemctl status docker --no-pager"
ansible all -m shell -a "systemctl status jenkins --no-pager"
```

---

## 🌐 Access Jenkins After Install

Open in browser:
```
http://YOUR_SERVER_IP:8080
```

Get the admin password:
```bash
ansible all -m shell -a "cat /var/lib/jenkins/secrets/initialAdminPassword"
```

---

## 🔧 Common Fixes

| Problem | Fix |
|---|---|
| `ping` fails | Check IP in `inventory/hosts.yml` and run `ssh-copy-id user@IP` |
| Permission denied | Run passwordless sudo setup (Step 1.4) |
| Python not found | `ansible all -m raw -a "sudo apt install python3 -y"` (Ubuntu) |
| Docker permission denied | Reboot the server: `ansible all -m shell -a "sudo reboot"` |
| Jenkins won't start | `ansible all -m shell -a "journalctl -u jenkins --no-pager -n 20"` |
| Want to re-run | Just run the same playbook again — Ansible skips what's already done |

---

## 🔄 Useful Extra Commands

```bash
# Reboot all servers
ansible all -m shell -a "sudo reboot"

# Check disk space
ansible all -m shell -a "df -h"

# Check memory
ansible all -m shell -a "free -m"

# Check OS version
ansible all -m shell -a "cat /etc/os-release"

# Run any command on all servers
ansible all -m shell -a "YOUR_COMMAND_HERE"
```

---

## ➕ Want to Add More Servers?

Just add them to `inventory/hosts.yml`:

```yaml
ubuntu:
  hosts:
    ubuntu-node-1:
      ansible_host: 192.168.1.10
    ubuntu-node-2:                      # ← add new server
      ansible_host: 192.168.1.11       # ← its IP
```

Then run:
```bash
ssh-copy-id user@192.168.1.11          # setup SSH
ansible all -m ping                     # test
ansible-playbook site.yml              # install everything
```

---

## 📦 What Gets Installed

| Tool | Version | What it does |
|---|---|---|
| **Docker** | Latest CE | Run containers |
| **Docker Compose** | Latest | Run multi-container apps |
| **Jenkins** | Latest LTS | CI/CD automation server |
| **Java 17** | OpenJDK | Required by Jenkins |
| **AWS CLI** | v2 (Latest) | Manage AWS from terminal |
| **Terraform** | Latest | Infrastructure as Code |

---

## 💡 Tips

- ✅ You can run playbooks **multiple times** — Ansible only changes what's needed
- ✅ Use `--check` to preview before applying
- ✅ Use `--limit` to target specific servers or groups
- ✅ Use `-v` for more output, `-vvv` for debug
- ✅ Logs are saved in `ansible.log`

---

**Made with ❤️ for DevOps automation**
