# 🚀 Ansible Master Node Setup (Ubuntu 24.x)

## 📌 Project Overview
📌 Project Overview

This project demonstrates a complete setup of an Ansible Master Node on Ubuntu, including Python installation, SSH configuration, inventory management, and execution of playbooks to manage remote slave nodes.

It simulates a real-world DevOps automation workflow, where a central master node controls multiple servers using agentless architecture.

---
## 🏗️ Architecture

```bash
[ Master Node ]
      |
      |  (SSH + Ansible)
      ↓
[ Slave Node(s) ]
Master Node: Runs Ansible
Slave Node(s): Managed via SSH
Communication: SSH (port 22)
```

## ⚙️ Prerequisites
- Ubuntu 22.04 / 24.04
- Sudo access
- 2 nodes (Master + Slave)

---

## 🐍 Step 1: Python Installation (Required)
Ansible requires Python.
```bash
sudo -i
apt update
apt install python3 -y
apt install python-is-python3 -y
python --version
```

---

## 📦 Step 2: Install Ansible
```bash
apt update
apt install ansible -y
ansible --version
```

---

## 📁 Step 3: Create Ansible Directory
```bash
mkdir -p /etc/ansible
touch /etc/ansible/hosts
```

---

## 🧾 Step 4: Create Inventory
```bash
vim hosts
```

```ini
[web]
<SLAVE_IP> ansible_user=ubuntu
```

---

## 🔑 Step 5: Setup SSH
```bash
ssh-keygen -t rsa

```
## 🔑 Step 6: Copy key in node
Access the nede -> 
```bash
cd .ssh
```
Insert the copied key 
```bash
vim authorised_key
```
---

## 🔍 Step 7: Test Connection
```bash
ansible -m ping <SLAVE_IP>
```

## 🎯 Outcome
- Master controls Slave via SSH
- Automated deployment using Ansible
