# 🚀 Ansible Master Node Setup (Ubuntu 24.x)

## 📌 Project Overview
This project demonstrates a complete setup of an Ansible Master Node on Ubuntu, including Python installation, SSH configuration, inventory management, and execution of playbooks.

---

## ⚙️ Prerequisites
- Ubuntu 22.04 / 24.04
- Sudo access
- 2 nodes (Master + Slave)

---

## 🐍 Step 1: Install Python
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

## 📁 Step 3: Create Project
```bash
mkdir ~/ansible-project
cd ~/ansible-project
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
ssh-keygen
ssh-copy-id ubuntu@<SLAVE_IP>
```

---

## 🔍 Step 6: Test Connection
```bash
ansible all -i hosts -m ping
```

---

## ⚙️ Step 7: Config File
```bash
vim ansible.cfg
```

```ini
[defaults]
inventory = hosts
host_key_checking = False
```

---

## 📜 Step 8: Playbook
```bash
vim nginx.yml
```

```yaml
- name: Install Nginx
  hosts: web
  become: yes

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes
```

---

## ▶️ Step 9: Run
```bash
ansible-playbook nginx.yml
```

---

## ✅ Step 10: Verify
```bash
ssh ubuntu@<SLAVE_IP>
systemctl status nginx
```

---

## 🎯 Outcome
- Master controls Slave via SSH
- Automated deployment using Ansible
