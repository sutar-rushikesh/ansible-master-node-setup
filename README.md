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

## Repository Structure

ansible-master-node-setup/
<p>|-- inventory.ini<p/>
|-- install_nginx.yml 
|-- create_ec2.yml      

--------------------------------------------------

## Inventory Configuration (IMPORTANT)

Correct inventory.ini format:

[ec2]
54.225.13.253 ansible_user=ubuntu ansible_ssh_private_key_file=/root/.ssh/mykey.pem

Rules:
- Host and variables must be on the SAME LINE
- Always use ABSOLUTE PATH for SSH key
- Do NOT use ~

--------------------------------------------------

## SSH Key Setup (Mandatory)

### Step 1: Copy key to Ansible master (if key is on local machine)
```bash
scp -i mykey.pem mykey.pem root@<ANSIBLE-MASTER-IP>:/root/.ssh/
```
### Step 2: Set correct permissions
```bash
chmod 400 /root/.ssh/mykey.pem
```
### Step 3: Verify SSH manually
```bash
ssh -i /root/.ssh/mykey.pem ubuntu@54.225.13.253
```
If this works, Ansible will work.

--------------------------------------------------

## Test Ansible Connectivity
```bash
ansible -i inventory.ini all -m ping
```
Expected output:
```bash
54.225.13.253 | SUCCESS => {
  "ping": "pong"
}
```
--------------------------------------------------

## Running Ad-hoc Ansible Commands

Check uptime:
```bash
ansible all -i inventory.ini -m shell -a "uptime"
```
Check disk usage:
```bash
ansible all -i inventory.ini -m shell -a "df -h"
```
Update packages:
```bash
ansible all -i inventory.ini -m shell -a "apt update -y"
```
--------------------------------------------------

## Install NGINX Using Playbook

File: install_nginx.yml
```bash
---
- name: Install NGINX on EC2 Node
  hosts: ec2
  become: yes

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Start and enable NGINX
      systemd:
        name: nginx
        state: started
        enabled: yes
```
Run playbook:
```bash
ansible-playbook -i inventory.ini install_nginx.yml
```
--------------------------------------------------

## Create EC2 Instance Using Ansible

### Prerequisites
```bash
pip3 install boto3 botocore
aws configure
```
### Playbook: create_ec2.yml
```bash
---
- name: Create EC2 instance using Ansible
  hosts: localhost
  connection: local

  vars:
    region: us-east-1
    instance_type: t2.micro
    ami_id: ami-0c55b159cbfafe1f0
    key_name: mykey
    security_group: default
    instance_name: Ansible-EC2-Demo

  tasks:
    - name: Launch EC2 instance
      amazon.aws.ec2_instance:
        name: "{{ instance_name }}"
        region: "{{ region }}"
        image_id: "{{ ami_id }}"
        instance_type: "{{ instance_type }}"
        key_name: "{{ key_name }}"
        security_group: "{{ security_group }}"
        wait: yes
      register: ec2_output

    - name: Show EC2 public IP
      debug:
        msg: "EC2 Public IP: {{ ec2_output.instances[0].public_ip_address }}"
```
Run playbook:
```bash
ansible-playbook create_ec2.yml
```
--------------------------------------------------

## Troubleshooting

### Error: Could not resolve hostname ansible_ssh_private_key_file
Cause:
Inventory file formatted incorrectly.

Fix:
Keep host and variables on the same line.

### Error: Identity file not accessible
Cause:
SSH key not present on Ansible master.

Fix:
Copy key to /root/.ssh/

### Error: Permission denied (publickey)

Possible fixes:
- Use correct SSH user: ubuntu / ec2-user
- Ensure correct key is used
- Ensure key permission is 400
- Verify keypair attached to EC2 instance

--------------------------------------------------

## Best Practices
- Always verify manual SSH before running Ansible
- Use group inventories
- Avoid using ~ in inventory files
- Secure keys and credentials
- Use Ansible Vault for secrets

--------------------------------------------------

## Conclusion
This document provides a complete reference for:
- Ansible master setup
- EC2 node management
- SSH troubleshooting
- Application installation
- EC2 provisioning automation

--------------------------------------------------

## Author:

Rushikesh Sutar

DevOps Engineer | CI/CD | Kubernetes | Cloud
