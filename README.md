# Ansible Automation

## Overview

This repository contains Ansible playbooks, inventories, roles, templates, and variables used to automate infrastructure management, application deployment, server configuration, and operational tasks.

Ansible is an agentless automation tool that communicates with managed nodes over SSH (Linux) or WinRM (Windows). It enables Infrastructure as Code (IaC) by automating repetitive administrative tasks.

---

# Features

* Agentless architecture
* Infrastructure as Code (IaC)
* Automated application deployment
* Configuration management
* Server provisioning
* Package installation
* Service management
* User and group management
* File and directory management
* Template management using Jinja2
* Multi-environment support
* Reusable roles
* Idempotent execution

# Prerequisites

Before using this project, ensure the following are installed:

* Python 3.x
* Ansible 2.15 or later
* SSH access to Linux servers
* WinRM configured for Windows servers (if applicable)

---

# Installation

## Ubuntu

```bash
sudo apt update
sudo apt install ansible -y
```

Verify installation

```bash
ansible --version
```

---

# Inventory Example

```ini
[webservers]
web01 ansible_host=192.168.1.10
web02 ansible_host=192.168.1.11

[databases]
db01 ansible_host=192.168.1.20
```

---

# Verify Connectivity

```bash
ansible all -m ping
```

Expected Output

```text
web01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
# Ansible Components

### Inventory

Defines the list of managed hosts and groups that Ansible will connect to and manage.

### Playbook

A YAML file containing one or more plays that define the tasks Ansible executes on managed hosts.

### Roles

A reusable and organized collection of tasks, handlers, templates, variables, and files used to automate a specific function.

### Tasks

Individual units of work executed by Ansible, such as installing packages, copying files, or starting services.

### Handlers

Special tasks that run only when notified by another task, typically used to restart or reload services after configuration changes.

### Modules

Built-in or custom programs that perform specific actions on managed hosts, such as package installation, file management, or service control.

### Variables (`vars`)

Store reusable values that make playbooks flexible and easier to maintain.

### Group Variables (`group_vars`)

Variables that are automatically applied to all hosts within a specific inventory group.

### Host Variables (`host_vars`)

Variables that are applied only to a specific host in the inventory.

### Templates

Jinja2 template files used to generate dynamic configuration files by replacing variables during playbook execution.

### Files

Contains static files such as scripts, certificates, or configuration files that can be copied to managed hosts.

### Defaults

Stores default variable values for a role. These values have the lowest precedence and can be overridden easily.

### Meta

Contains role metadata such as dependencies, supported platforms, and author information.

### Handlers

Contains all handler definitions that are triggered only when notified by tasks.

### Facts

System information automatically gathered by Ansible about managed hosts, including OS, IP address, memory, CPU, hostname, and more.

### Tags

Allow you to execute or skip specific tasks within a playbook without running the entire playbook.

### Templates (`.j2`)

Dynamic configuration files that use Jinja2 syntax to replace variables during deployment.

### Collections

A distribution format that packages Ansible modules, plugins, roles, and documentation together.

### Ansible Galaxy

The official repository for downloading and sharing Ansible roles and collections.

### Vault

Encrypts sensitive information such as passwords, API keys, and secrets to keep them secure.

### Handlers vs Tasks

Tasks run every time the playbook executes, whereas handlers run only when they are notified by a task that has made a change.

# Running Playbooks

Execute a playbook

```bash
ansible-playbook playbooks/install_nginx.yml
```

Specify inventory

```bash
ansible-playbook -i inventory/production playbooks/deploy_application.yml
```

Limit execution

```bash
ansible-playbook deploy.yml --limit webservers
```

Run as sudo

```bash
ansible-playbook deploy.yml --become
```

Dry Run

```bash
ansible-playbook deploy.yml --check
```

Show Differences

```bash
ansible-playbook deploy.yml --diff
```

---

# Common Ad-hoc Commands

### Ping Servers

```bash
ansible all -m ping
```

### Gather Facts

```bash
ansible all -m setup
```

### Check Uptime

```bash
ansible all -a "uptime"
```

### Install Package

```bash
ansible webservers -m yum -a "name=httpd state=present"
```

### Restart Service

```bash
ansible webservers -m service -a "name=httpd state=restarted"
```

### Copy File

```bash
ansible all -m copy -a "src=test.txt dest=/tmp/test.txt"
```

### Remove File

```bash
ansible all -m file -a "path=/tmp/test.txt state=absent"
```

---

# Variables

## group_vars

```yaml
http_port: 80
app_name: myapp
```

## host_vars

```yaml
server_name: web01
```

Use variables

```yaml
- name: Print server name
  debug:
    msg: "{{ server_name }}"
```

---

# Templates

Example

```jinja2
server {
    listen {{ http_port }};
    server_name {{ server_name }};
}
```

Deploy template

```yaml
- name: Deploy nginx configuration
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

---

# Roles

Example

```text
roles/
└── nginx
    ├── tasks
    ├── handlers
    ├── defaults
    ├── vars
    ├── files
    ├── templates
    └── meta
```

Execute role

```yaml
---
- hosts: webservers
  become: yes

  roles:
    - nginx
```

---

# Handlers

```yaml
handlers:

- name: Restart nginx
  service:
    name: nginx
    state: restarted
```

Notify handler

```yaml
notify:
  - Restart nginx
```

---

# Best Practices

* Use roles for reusable automation.
* Store secrets using Ansible Vault.
* Use meaningful inventory groups.
* Keep playbooks idempotent.
* Separate environments (Development, Staging, Production).
* Use templates instead of hardcoded configuration files.
* Keep variables in `group_vars` and `host_vars`.
* Test playbooks with `--check` before production deployment.
* Use tags for selective execution.
* Maintain version control with Git.

---

# Useful Options

| Option        | Description         |
| ------------- | ------------------- |
| `-i`          | Inventory file      |
| `-l`          | Limit hosts         |
| `--check`     | Dry run             |
| `--diff`      | Show changes        |
| `--tags`      | Run specific tags   |
| `--skip-tags` | Skip tags           |
| `-K`          | Ask become password |
| `-u`          | Remote user         |
| `-v`          | Verbose             |
| `-vvv`        | Debug mode          |

---

# Ansible Execution Flow

```text
Developer
      │
      ▼
Git Repository
      │
      ▼
Ansible Control Node
      │
      ├────────SSH────────► Linux Servers
      │
      └──────WinRM───────► Windows Servers
      │
      ▼
Tasks
      │
      ▼
Modules
      │
      ▼
Managed Nodes
```

---

# Security Best Practices

* Never store passwords in plain text.
* Use Ansible Vault for secrets.
* Restrict SSH key access.
* Enable logging.
* Use least-privilege accounts.
* Rotate credentials regularly.
* Validate playbooks before production.

---

# Useful Commands

```bash
ansible --version

ansible-config dump

ansible-inventory --list

ansible-doc yum

ansible-galaxy install geerlingguy.nginx

ansible-playbook site.yml

ansible-playbook site.yml --check

ansible-playbook site.yml --diff

ansible-playbook site.yml --syntax-check
```

---

# Learning Topics

* Inventory
* Playbooks
* Modules
* Variables
* Facts
* Loops
* Conditionals
* Roles
* Templates
* Handlers
* Tags
* Vault
* Collections
* Galaxy
* Dynamic Inventory
* Error Handling
* Blocks
* Includes
* Imports
* Delegation
* Async Tasks

---

# Author

**Pradeep Kumar Chitroliya**

**Sr DevOps Engineer**

---

# License

This project is licensed under the MIT License.
