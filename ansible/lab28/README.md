# Lab 28 -- Structured Configuration Management with Ansible Roles

## Overview

This project demonstrates structured configuration management using
Ansible roles. Separate roles were created to install and configure:

-   Docker
-   Kubernetes CLI (kubectl)
-   Jenkins

All roles are executed using a single Ansible playbook on an AWS EC2
managed node.

------------------------------------------------------------------------

## Project Structure
```bash
ansible/
├── inventory.ini
├── playbook.yaml
└── roles/
    ├── docker/
    ├── kubectl/
    └── jenkins/
```
>Each role was initialized using:
```bash
ansible-galaxy init <role-name> 
```

------------------------------------------------------------------------

## AWS Infrastructure Requirements

### EC2 Configuration

-   `AMI: Ubuntu Server`
-   `Instance Type: t2.micro / t3.micro`
-   `Key Pair: SSH key-based authentication`
-   `Security Group inbound rules:`

    | Port | Protocol | Purpose        |
    |:----:|:--------:|---------------|
    | 22   | TCP      | SSH Access     |
    | 8080 | TCP      | Jenkins Web UI |

------------------------------------------------------------------------

## Inventory File

inventory.ini
```ini
[managed]
 ec2 ansible_host=<EC2-PUBLIC-IP> ansible_user=ubuntu
\ansible_ssh_private_key_file=\~/.ssh/<YOUR-PEM-KEY>
```
------------------------------------------------------------------------

## How to Run
```bash
ansible-playbook -i inventory.ini playbook.yaml
```

------------------------------------------------------------------------

## Verification Commands
```bash
docker --version kubectl version --client systemctl status jenkins
```
------------------------------------------------------------------------

## Access Jenkins

Open in browser:
```bash
http://<EC2_PUBLIC_IP>:8080
```
Get initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
------------------------------------------------------------------------

## ScreenShots

![Lab 28 Result](lab28(1).png)

![Lab 28 Result](lab28(2).png)

![Lab 28 Result](lab28(3).png)

----
## Author

Mohamed Ahmed Mohamed Taha
