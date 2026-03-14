# TP2 Lab Report - Ansible Automation Practice

## 1. Student Information
- Name: BUN Sengleang
- Class/Group: I4C
- Lab Title: Ansible Remote Provisioning and Python Environment Setup
- Date: 2026-03-14

## 2. Lab Objectives
This lab aimed to:
1. Configure Ansible inventory and connectivity to a remote Linux server.
2. Validate remote access using ad-hoc ping and playbook ping.
3. Automate server preparation using an Ansible playbook.
4. Install Python tooling and dependencies inside a virtual environment.
5. Practice troubleshooting YAML and SSH authentication issues.

## 3. Environment and Tools
- Control node OS: macOS (Darwin)
- Automation tool: Ansible Core 2.20.3
- Python used by Ansible: 3.14.3
- Remote host group: `staging`
- Remote target alias: `host1`
- Remote IP: `152.42.188.57`
- Remote SSH user: `root`

## 4. Project Structure Review
The TP2 folder contains:
- `inventory.ini`: inventory definition for host group `staging`.
- `ping.yaml`: basic connectivity playbook.
- `playbook.yaml`: main setup/provisioning playbook.
- `install-nginx.yaml`: extra playbook to install Nginx.
- `requirements.txt`: Python packages to install in remote virtual environment.
- `ansible.cfg`: local Ansible defaults (`ask_pass`, inventory path, interpreter warning suppression).
- Lab screenshots showing commands and outputs.

## 5. Key Configuration Files

### 5.1 Inventory (`inventory.ini`)
- Defines group `[staging]`.
- Includes host `host1` mapped to `152.42.188.57` on port 22.
- Uses user `root` and disables strict host key checking for this lab.

### 5.2 Main Playbook (`playbook.yaml`)
The playbook performs these tasks on hosts in `staging`:
1. Ping server (`ansible.builtin.ping`).
2. Install system packages: `python3-venv`, `python3-pip`.
3. Create `/root/devops` directory.
4. Copy `requirements.txt` to remote server.
5. Create Python virtual environment at `/root/devops/venv`.
6. Install Python packages from requirements file inside the virtual environment.

### 5.3 Connectivity Playbook (`ping.yaml`)
- Minimal playbook to validate managed host connectivity before full deployment.

### 5.4 Optional Playbook (`install-nginx.yaml`)
- Installs Nginx with apt on the same target group.

### 5.5 Local Ansible Config (`ansible.cfg`)
- `inventory = inventory.ini`
- `ask_pass = True`
- `host_key_checking = False`
- `interpreter_python = auto_silent`

## 6. Execution Timeline and Troubleshooting

### 6.1 YAML Parsing Error
Initial execution failed due to YAML formatting/indentation problems in `playbook.yaml` (missing proper nesting under the play list item). This was corrected by fixing indentation and structure.

### 6.2 SSH Authentication Error
After syntax fixes, execution failed with:
- `Permission denied (publickey,password)`

Root cause:
- Running without password prompt made Ansible try key-based auth by default.

Resolution:
- Running with `--ask-pass` succeeded.
- Added `ansible.cfg` with `ask_pass = True` so the project prompts for password by default.

### 6.3 Python Interpreter Warning
Warning about discovered interpreter (`/usr/bin/python3.12`) was shown.
- Mitigation in this lab: `interpreter_python = auto_silent` in `ansible.cfg`.

## 7. Command Results Summary

### 7.1 Connectivity Tests
- `ansible all -m ping -i inventory.ini --ask-pass`: SUCCESS (`pong`).
- `ansible-playbook -i inventory.ini ping.yaml --ask-pass`: SUCCESS.

### 7.2 Main Deployment
- `ansible-playbook -i inventory.ini playbook.yaml --ask-pass`: SUCCESS.
- Final recap observed: `ok=7`, `changed=2`, `failed=0`, `unreachable=0`.

### 7.3 Useful Note on Host Limiting
- `--limit 152.42.188.57` failed because inventory hostname is `host1`.
- Correct usage: `--limit host1` or `--limit staging`.

## 8. Evidence (Screenshots)
Insert captions below each screenshot to explain what command/output is shown.

### Screenshot 1
![Screenshot 1](Screenshot%202026-03-14%20at%2010.52.41%20at%20night.png)

### Screenshot 2
![Screenshot 2](Screenshot%202026-03-14%20at%2010.51.42%20at%20night.png)

### Screenshot 3
![Screenshot 3](Screenshot%202026-03-14%20at%2010.59.56%20at%20night.png)

### Screenshot 4
![Screenshot 4](Screenshot%202026-03-14%20at%2011.01.36%20at%20night.png)

### Screenshot 5
![Screenshot 5](Screenshot%202026-03-14%20at%2011.06.57%20at%20night.png)

### Screenshot 6
![Screenshot 6](Screenshot%202026-03-14%20at%2011.11.34%20at%20night.png)

## 9. Conclusion
The lab successfully demonstrated remote automation with Ansible from a macOS control node to a Linux target host. After resolving YAML indentation issues and SSH authentication mode mismatch, the main playbook executed correctly and provisioned a Python virtual environment with required packages. This confirms idempotent and reproducible environment setup using Ansible playbooks.
