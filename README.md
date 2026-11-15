# 🚀 Ansible Multi-OS DevOps Tools Installer

> Automated installation of **Docker**, **Jenkins**, **AWS CLI v2**, and **Terraform** across multi-OS slave nodes — **Ubuntu**, **Amazon Linux**, and **RedHat/CentOS**.

---

## 📁 Project Structure

```
ansible/
├── ansible.cfg                    # Ansible configuration
├── site.yml                       # Master playbook (installs ALL tools)
├── inventory/
│   └── hosts.yml                  # Inventory with OS-based groups
├── group_vars/
│   ├── all.yml                    # Global variables for all hosts
│   ├── ubuntu.yml                 # Ubuntu-specific variables
│   ├── amazon_linux.yml           # Amazon Linux-specific variables
│   └── redhat.yml                 # RedHat-specific variables
├── playbooks/
│   ├── docker.yml                 # Standalone: install only Docker
│   ├── jenkins.yml                # Standalone: install only Jenkins
│   ├── awscli.yml                 # Standalone: install only AWS CLI
│   └── terraform.yml              # Standalone: install only Terraform
└── roles/
    ├── docker/
    │   ├── tasks/
    │   │   ├── main.yml           # Entry point — routes to OS-specific tasks
    │   │   ├── ubuntu.yml         # Docker install for Ubuntu (apt + Docker repo)
    │   │   ├── amazon_linux.yml   # Docker install for Amazon Linux (yum/extras)
    │   │   └── redhat.yml         # Docker install for RHEL/CentOS (dnf + Docker repo)
    │   ├── handlers/main.yml      # Restart Docker handler
    │   ├── defaults/main.yml      # Default variables
    │   └── meta/main.yml          # Role metadata
    ├── jenkins/
    │   ├── tasks/
    │   │   ├── main.yml           # Entry point — installs Java first, then Jenkins
    │   │   ├── install_java.yml   # Java 17 installation (all OS)
    │   │   ├── ubuntu.yml         # Jenkins install for Ubuntu
    │   │   ├── amazon_linux.yml   # Jenkins install for Amazon Linux
    │   │   └── redhat.yml         # Jenkins install for RHEL/CentOS
    │   ├── handlers/main.yml      # Restart Jenkins handler
    │   ├── defaults/main.yml      # Default variables (port, java opts)
    │   └── meta/main.yml          # Role metadata
    ├── awscli/
    │   ├── tasks/main.yml         # AWS CLI v2 binary installer (all OS)
    │   ├── handlers/main.yml
    │   ├── defaults/main.yml
    │   └── meta/main.yml
    └── terraform/
        ├── tasks/
        │   ├── main.yml           # Entry point — routes to OS-specific tasks
        │   ├── ubuntu.yml         # Terraform install for Ubuntu (HashiCorp apt repo)
        │   ├── amazon_linux.yml   # Terraform install for Amazon Linux (HashiCorp yum repo)
        │   └── redhat.yml         # Terraform install for RHEL/CentOS (HashiCorp dnf repo)
        ├── handlers/main.yml
        ├── defaults/main.yml
        └── meta/main.yml
```

---

## ✅ Prerequisites

### On the Control Node (your machine)

| Requirement | Install Command |
|---|---|
| **Ansible ≥ 2.12** | `sudo apt install ansible` or `pip install ansible` |
| **Python 3.x** | Usually pre-installed |
| **SSH key pair** | `ssh-keygen -t rsa -b 4096` |

### On Each Slave Node

| Requirement | Details |
|---|---|
| **SSH access** | Password-less SSH from control node |
| **sudo privileges** | The `ansible` user must have passwordless sudo |
| **Python 3** | Required by Ansible to run modules |

### Setting Up SSH Access

```bash
# Generate SSH key (if you don't have one)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""

# Copy public key to each slave node
ssh-copy-id ansible@192.168.1.10   # ubuntu-node-1
ssh-copy-id ansible@192.168.1.11   # ubuntu-node-2
ssh-copy-id ansible@192.168.1.20   # amazon-node-1
ssh-copy-id ansible@192.168.1.21   # amazon-node-2
ssh-copy-id ansible@192.168.1.30   # redhat-node-1
ssh-copy-id ansible@192.168.1.31   # redhat-node-2
```

### Setting Up Passwordless Sudo on Slave Nodes

```bash
# Run on each slave node
echo "ansible ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/ansible
sudo chmod 0440 /etc/sudoers.d/ansible
```

---

## ⚙️ Configuration

### 1. Update the Inventory

Edit `inventory/hosts.yml` and replace the IP addresses with your actual server IPs:

```yaml
all:
  children:
    ubuntu:
      hosts:
        ubuntu-node-1:
          ansible_host: <YOUR_UBUNTU_IP_1>
        ubuntu-node-2:
          ansible_host: <YOUR_UBUNTU_IP_2>

    amazon_linux:
      hosts:
        amazon-node-1:
          ansible_host: <YOUR_AMAZON_IP_1>

    redhat:
      hosts:
        redhat-node-1:
          ansible_host: <YOUR_REDHAT_IP_1>
```

### 2. Update Global Variables

Edit `group_vars/all.yml` to customize:

| Variable | Default | Description |
|---|---|---|
| `ansible_user` | `ansible` | SSH user on remote nodes |
| `ansible_ssh_private_key_file` | `~/.ssh/id_rsa` | Path to SSH private key |
| `terraform_version` | `1.9.8` | Terraform version to install |
| `jenkins_http_port` | `8080` | Jenkins web UI port |
| `docker_compose_install` | `true` | Install Docker Compose |

> [!TIP]
> For AWS EC2 instances, the default SSH user is typically:
> - **Ubuntu**: `ubuntu`
> - **Amazon Linux**: `ec2-user`
> - **RedHat**: `ec2-user`
>
> Override per group in `group_vars/<group>.yml`.

---

## 🚀 Usage & Commands

### Test Connectivity First

```bash
# Ping all slave nodes
ansible slave_nodes -m ping

# Ping only Ubuntu nodes
ansible ubuntu -m ping

# Ping only Amazon Linux nodes
ansible amazon_linux -m ping

# Ping only RedHat nodes
ansible redhat -m ping
```

### Install ALL Tools on ALL Nodes

```bash
ansible-playbook site.yml
```

### Install Individual Tools

```bash
# Install only Docker on all nodes
ansible-playbook playbooks/docker.yml

# Install only Jenkins on all nodes
ansible-playbook playbooks/jenkins.yml

# Install only AWS CLI on all nodes
ansible-playbook playbooks/awscli.yml

# Install only Terraform on all nodes
ansible-playbook playbooks/terraform.yml
```

### Target Specific OS Groups

```bash
# Install everything on Ubuntu nodes only
ansible-playbook site.yml --limit ubuntu

# Install Docker on RedHat nodes only
ansible-playbook playbooks/docker.yml --limit redhat

# Install Jenkins on Amazon Linux only
ansible-playbook playbooks/jenkins.yml --limit amazon_linux
```

### Target a Single Host

```bash
# Install all tools on a specific node
ansible-playbook site.yml --limit ubuntu-node-1

# Install Docker on a specific node
ansible-playbook playbooks/docker.yml --limit redhat-node-1
```

### Dry Run (Check Mode)

```bash
# Preview changes without actually applying them
ansible-playbook site.yml --check

# Preview with verbose output
ansible-playbook site.yml --check -v
```

### Verbose Execution

```bash
# Normal verbosity
ansible-playbook site.yml -v

# More verbose (connection debugging)
ansible-playbook site.yml -vv

# Maximum verbosity
ansible-playbook site.yml -vvv
```

---

## 📦 Role Details

### 🐳 Docker Role

**What it installs:** Docker CE (Community Edition), Docker CLI, containerd, Docker Buildx, and optionally Docker Compose.

| OS | Method |
|---|---|
| **Ubuntu** | Official Docker apt repository |
| **Amazon Linux 2** | `amazon-linux-extras install docker` |
| **Amazon Linux 2023** | `yum install docker` |
| **RedHat/CentOS** | Official Docker CE yum/dnf repository |

**Post-install actions:**
- Starts & enables Docker service
- Adds the ansible user to the `docker` group
- Installs Docker Compose (configurable)
- Verifies installation with `docker --version`

---

### 🏗️ Jenkins Role

**What it installs:** Jenkins LTS, OpenJDK 17 (prerequisite)

| OS | Method |
|---|---|
| **Ubuntu** | Official Jenkins apt repository |
| **Amazon Linux** | Official Jenkins yum repository |
| **RedHat/CentOS** | Official Jenkins yum/dnf repository |

**Post-install actions:**
- Starts & enables Jenkins service
- Waits for Jenkins to be ready on port 8080
- Retrieves and displays the initial admin password

**Accessing Jenkins after install:**
```
http://<node-ip>:8080
```

---

### ☁️ AWS CLI Role

**What it installs:** AWS CLI v2 (latest)

| OS | Method |
|---|---|
| **All Linux** | Official AWS CLI v2 binary installer (arch-aware: x86_64 / aarch64) |

**Post-install actions:**
- Cleans up installer files
- Verifies installation with `aws --version`

**Configure AWS credentials after install:**
```bash
aws configure
# Enter: AWS Access Key ID, Secret Access Key, Region, Output format
```

---

### 🏗️ Terraform Role

**What it installs:** Terraform (from HashiCorp official repositories)

| OS | Method |
|---|---|
| **Ubuntu** | HashiCorp apt repository |
| **Amazon Linux** | HashiCorp yum repository |
| **RedHat/CentOS** | HashiCorp yum/dnf repository |

**Post-install actions:**
- Verifies installation with `terraform version`

---

## 🔧 Advanced Usage

### Run with a Different SSH User

```bash
ansible-playbook site.yml -u ec2-user --private-key ~/.ssh/my-key.pem
```

### Run with SSH Password (not recommended)

```bash
ansible-playbook site.yml --ask-pass --ask-become-pass
```

### Run Specific Roles Using Tags

You can add tags to the `site.yml` and run selectively:

```bash
# Edit site.yml to add tags:
#   roles:
#     - { role: docker, tags: docker }
#     - { role: jenkins, tags: jenkins }
#     - { role: awscli, tags: awscli }
#     - { role: terraform, tags: terraform }

# Then run:
ansible-playbook site.yml --tags "docker,terraform"
ansible-playbook site.yml --skip-tags "jenkins"
```

### Check Installed Versions (Ad-Hoc)

```bash
# Check Docker version on all nodes
ansible slave_nodes -m shell -a "docker --version"

# Check Jenkins status on all nodes
ansible slave_nodes -m shell -a "systemctl status jenkins"

# Check AWS CLI version
ansible slave_nodes -m shell -a "aws --version"

# Check Terraform version
ansible slave_nodes -m shell -a "terraform version"
```

---

## 🔍 Troubleshooting

### Common Issues

| Issue | Solution |
|---|---|
| **SSH connection refused** | Ensure SSH is running: `systemctl status sshd` |
| **Permission denied** | Check SSH key is copied: `ssh-copy-id user@host` |
| **Sudo password required** | Set up passwordless sudo (see Prerequisites) |
| **Python not found** | Install Python 3: `sudo apt install python3` or `sudo yum install python3` |
| **Package manager locked** | Wait or kill stuck processes: `sudo killall apt-get` |
| **Jenkins won't start** | Check Java: `java -version`, check logs: `journalctl -u jenkins` |
| **Docker permission denied** | Re-login after adding user to docker group, or reboot |

### View Ansible Logs

```bash
# Logs are saved to ansible.log (configured in ansible.cfg)
cat ansible.log

# Tail logs in real-time during execution
tail -f ansible.log
```

### Debug a Specific Host

```bash
ansible-playbook site.yml --limit ubuntu-node-1 -vvv
```

---

## 📋 Verification Checklist

After running the playbook, verify on each node:

```bash
# Docker
docker --version
docker run hello-world
systemctl status docker

# Jenkins
systemctl status jenkins
curl -s http://localhost:8080 | head -5

# AWS CLI
aws --version

# Terraform
terraform version
```

---

## 🏗️ Extending This Project

### Adding a New Tool

1. Create a new role:
   ```bash
   mkdir -p roles/newtool/tasks roles/newtool/handlers roles/newtool/defaults roles/newtool/meta
   ```

2. Create OS-specific task files in `roles/newtool/tasks/`:
   - `main.yml` — routes to the right OS file
   - `ubuntu.yml`, `amazon_linux.yml`, `redhat.yml`

3. Add the role to `site.yml`:
   ```yaml
   roles:
     - docker
     - jenkins
     - awscli
     - terraform
     - newtool    # ← add here
   ```

4. Create a standalone playbook in `playbooks/newtool.yml`

### Adding a New OS Group

1. Add hosts to `inventory/hosts.yml` under a new group
2. Add that group as a child of `slave_nodes`
3. Create `group_vars/<new_group>.yml`
4. Add OS-specific task files to each role

---

## 📜 License

This project is open source. Feel free to modify and distribute.

## 👤 Author

DevOps Team — Multi-OS Infrastructure Automation
