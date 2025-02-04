# Ansible Playbook for Raspberry Pi Setup

## Overview
This Ansible playbook logs into six different Raspberry Pis, installs necessary programs, and configures the system automatically.

## Inventory File (`inventory.ini`)
This file contains the list of Raspberry Pis and their SSH connection details.

```ini
[raspberry_pis]
Worker1 ansible_host=192.168.1.xxx
Worker2 ansible_host=192.168.1.xxx
Worker3 ansible_host=192.168.1.xxx
Worker4 ansible_host=192.168.1.xxx
Worker5 ansible_host=192.168.1.xxx
Worker6 ansible_host=192.168.1.xxx

[raspberry_pis:vars]
ansible_user=pi
ansible_password=your_password
ansible_become_pass=your_password
ansible_python_interpreter=/usr/bin/python3
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
ansible_become=true
```

### Explanation
- `[raspberry_pis]` - Defines the group containing all Raspberry Pis.
- `WorkerX ansible_host=192.168.1.xxx` - Each Raspberry Pi is identified with a name (`WorkerX`) and its IP address.
- `[raspberry_pis:vars]` - Defines variables for the group.
- `ansible_user=pi` - The username to use for SSH.
- `ansible_password=your_password` - The SSH password (replace with the actual password).
- `ansible_become_pass=your_password` - The sudo password.
- `ansible_python_interpreter=/usr/bin/python3` - Ensures Python 3 is used.
- `ansible_ssh_common_args='-o StrictHostKeyChecking=no'` - Avoids SSH key confirmation prompts.
- `ansible_become=true` - Enables sudo access.

## Ansible Playbook (`install_dependencies.yml`)
This playbook updates the system, installs `python3-pip`, and installs PyTorch and TorchVision.

```yaml
---
- name: Install dependencies on Raspberry Pis
  hosts: raspberry_pis
  become: yes

  tasks:
    - name: Update package list
      apt:
        update_cache: yes

    - name: Install Python3-pip
      apt:
        name: python3-pip
        state: present

    - name: Install PyTorch and torchvision
      pip:
        name:
          - torch
          - torchvision
        executable: pip3
```

### Explanation
- `hosts: raspberry_pis` - Targets all hosts in the `[raspberry_pis]` group.
- `become: yes` - Enables sudo.
- `tasks:` - Defines tasks to execute:
  - `apt: update_cache: yes` - Runs `sudo apt update`.
  - `apt: name: python3-pip` - Installs `python3-pip`.
  - `pip: name: torch, torchvision` - Installs PyTorch and TorchVision using `pip3`.

## Running the Playbook
To execute the playbook, run:

```bash
ansible-playbook -i inventory.ini install_dependencies.yml --ask-become-pass
```

or if you want to store logs

```bash
ansible-playbook -i inventory.ini install_dependencies.yml --ask-become-pass | tee ansible_log.txt
```

### Command Breakdown
- `ansible-playbook` - Runs an Ansible playbook.
- `-i inventory.ini` - Specifies the inventory file.
- `install_dependencies.yml` - The playbook to execute.
- `--ask-become-pass` - Prompts for the sudo password (optional if already set in `inventory.ini`).

## Notes
- Ensure SSH access works before running Ansible (`ssh pi@192.168.1.xxx`).
- If you don't want to store passwords in `inventory.ini`, remove `ansible_password` and `ansible_become_pass`, and enter them when prompted.

---
This file serves as a reference to remind you how to set up and use this Ansible playbook efficiently.


This is the command to run the DNS settings change playbook

```bash
ansible-playbook -i inventory.ini update_dns_settings.yml --ask-become-pass | tee ansible_log_DNS.txt
```
