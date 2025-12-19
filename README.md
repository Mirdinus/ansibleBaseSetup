# ansibleBaseSetup
Base setup for debian/ubuntu based servers with hardening and quality of life improvements.

## Overview
This Ansible project automates the initial setup and hardening of Ubuntu/Debian servers. It performs the following tasks:

- Copies SSH authorized_keys for the ubuntu user
- Updates and upgrades the system
- Installs htop for system monitoring
- Installs and configures fail2ban
- Hardens SSH configuration (disables password auth, root login)
- Removes nginx and apache2 if installed
- Configures UFW firewall with secure defaults

## Prerequisites

- Ansible 2.9 or higher
- SSH access to the target server(s)
- User with sudo privileges (default: ubuntu)

## Local Usage

### Installation

1. Clone this repository:
```bash
git clone https://github.com/Mirdinus/ansibleBaseSetup.git
cd ansibleBaseSetup
```

2. Install Ansible and required collections:
```bash
pip install ansible
ansible-galaxy collection install -r requirements.yml
```

### Running the Playbook

1. Create an inventory file with your server IP addresses:
```bash
cat > inventory.ini << EOF
[servers]
server1 ansible_host=192.168.1.10
EOF
```

2. Run the playbook:
```bash
ansible-playbook -i inventory.ini playbook.yml
```

### Running Specific Tasks

You can run specific tasks using tags:
```bash
# Only update packages
ansible-playbook playbook.yml --tags update

# Only configure firewall
ansible-playbook playbook.yml --tags firewall

# Only harden SSH
ansible-playbook playbook.yml --tags ssh
```

Available tags: `ssh`, `setup`, `update`, `upgrade`, `packages`, `security`, `fail2ban`, `cleanup`, `firewall`

## GitHub Actions Usage

This repository includes a GitHub Actions workflow that can be triggered manually to deploy to a server.

### Setup

1. Add your SSH private key to GitHub Secrets:
   - Go to your repository Settings > Secrets and variables > Actions
   - Create a new secret named `SSH_PRIVATE_KEY`
   - Paste your private SSH key content

### Running the Workflow

1. Go to the "Actions" tab in your GitHub repository
2. Select "Deploy Ansible Playbook" workflow
3. Click "Run workflow"
4. Enter the target server IP address
5. Optionally change the SSH user (default: ubuntu)
6. Click "Run workflow"

The workflow will:
- Use a Docker container with Ansible pre-installed
- Install required Ansible collections
- Create a dynamic inventory with your server IP
- Run the playbook against your server
- Clean up sensitive data

## Firewall Configuration

The playbook configures UFW with the following rules:
- Default deny incoming
- Default allow outgoing
- Allow SSH (port 22/tcp)

## SSH Hardening

The playbook applies the following SSH hardening measures:
- Disables password authentication
- Disables root login
- Disables empty passwords
- Enforces SSH Protocol 2

## Security Notes

- Ensure you have SSH key-based authentication set up before running this playbook
- The playbook disables password authentication - make sure your SSH keys are properly configured
- Test the playbook in a non-production environment first
- Keep your SSH private key secure and never commit it to the repository

## Troubleshooting

If you encounter issues:
1. Verify SSH connectivity: `ssh ubuntu@<server_ip>`
2. Check Ansible can reach the host: `ansible all -m ping -i <your_inventory_file>`
3. Run playbook in verbose mode: `ansible-playbook -i <your_inventory_file> playbook.yml -vvv`

## License

MIT
