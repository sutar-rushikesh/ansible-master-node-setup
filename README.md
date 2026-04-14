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
```
```bash
apt update
```
```bash
apt install python3 -y
```
```bash
apt install python-is-python3 -y
```
```bash
python --version
```

---

## 📦 Step 2: Install Ansible
```bash
apt update
```
```bash
apt install ansible -y
```
```bash
ansible --version
```

---

## 📁 Step 3: Create Ansible Directory
```bash
mkdir -p /etc/ansible
```
```bash
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
<p>|-- inventory.ini <p/>
<p>|-- install_nginx.yml <p/>
<p>|-- create_ec2.yml    <p/>  
--------------------------------------------------

## Inventory Configuration (IMPORTANT)

Correct inventory.ini format:
```bash
[ec2]
<NODE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=/root/.ssh/mykey.pem
```
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
or create file vim mykey.pem in /root/.ssh

### Step 2: Set correct permissions
```bash
chmod 400 /root/.ssh/mykey.pem
```
### Step 3: Verify SSH manually
```bash
ssh -i /root/.ssh/mykey.pem ubuntu@<NODE_IP>
```
If this works, Ansible will work.

--------------------------------------------------

## Test Ansible Connectivity
```bash
ansible -i inventory.ini all -m ping
```
Expected output:
```bash
<NODE_IP> | SUCCESS => {
  "ping": "pong"
}
```
<img width="1920" height="217" alt="1" src="https://github.com/user-attachments/assets/a09ce3c5-e740-4937-9c0f-6c8859a38540" />

--------------------------------------------------

## Running Ad-hoc Ansible Commands

Check uptime:
```bash
ansible all -i inventory.ini -m shell -a "uptime"
```
<img width="1920" height="120" alt="2" src="https://github.com/user-attachments/assets/3e3c30fe-4b9d-4acb-9c64-b2ee37ab6e12" />


Check disk usage:
```bash
ansible all -i inventory.ini -m shell -a "df -h"
```
<img width="1920" height="271" alt="3" src="https://github.com/user-attachments/assets/3d6980fc-fe73-44df-99dc-e706c9b296d7" />


Update packages:
```bash
ansible all -i inventory.ini -m shell -a "apt update -y" -b
```
<img width="1920" height="356" alt="image" src="https://github.com/user-attachments/assets/e6b9bcee-70bd-4bf2-8380-c2aaa675d9ff" />


To Copy File from master to node 
```bash
ansible all -m copy -a "src=/etc/ansible/hosts dest=/var"
```

<img width="1920" height="462" alt="image" src="https://github.com/user-attachments/assets/e7f454d7-9531-4fad-b155-a306d1ede7c5" />


Install or update Git to the latest version on all servers using sudo.
```bash
ansible all -m apt -a "name=git state=latest" -b
```
<img width="1920" height="247" alt="image" src="https://github.com/user-attachments/assets/4fcb48a6-0ecf-414f-80b8-212a6118b72c" />


Run ls -lrt / on all servers and display the root directory contents.
```bash
ansible all -m shell -a "ls -lrt /" 
```
<img width="1917" height="668" alt="image" src="https://github.com/user-attachments/assets/977f5993-dfd2-4529-849b-067df907e500" />

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
<img width="1920" height="455" alt="4" src="https://github.com/user-attachments/assets/daed8516-7b39-4eb2-8a6d-c77411f57e31" />

--------------------------------------------------

## Create EC2 Instance Using Ansible

### Prerequisites
```bash
apt install python3-pip
```
```bash
pip3 install boto3 botocore
```
To install the AWS CLI (Command Line Interface) on Ubuntu, follow these steps:

1. Update the Package Index
Run the following command to ensure your package list is up-to-date:
```bash
sudo apt update
```
2. Install Dependencies
Ensure you have the necessary dependencies installed:
```bash
sudo apt install -y unzip curl
```
3. Download the AWS CLI Installer
Use curl to download the AWS CLI v2 installer:
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
4. Extract the Installer
Unzip the downloaded file:
```bash
unzip awscliv2.zip
```
5. Run the Installer
Run the installation script:
```bash
sudo ./aws/install
```
6. Verify the Installation
Confirm that the AWS CLI is installed and check its version:
```bash
aws --version
```
You should see output similar to:

aws-cli/2.x.x Python/x.x.x Linux/x.x.x

7. Configure the AWS CLI
Set up your AWS credentials:
```bash
aws configure
```
You’ll need to provide:
<p?
Access Key ID
Secret Access Key
Default region (e.g., us-east-1)
Output format (e.g., json, text, or table) <p/>
```
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
<img width="1919" height="880" alt="Screenshot 2026-04-14 101924" src="https://github.com/user-attachments/assets/71af98f0-27e3-4a0e-9a76-0acda3dead8d" />
<img width="1914" height="862" alt="Screenshot 2026-04-14 101950" src="https://github.com/user-attachments/assets/87508ef1-4023-4a5d-97cb-e0ef689c59ea" />

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
