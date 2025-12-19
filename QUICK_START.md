# Quick Start Guide

## Using GitHub Actions (Recommended)

1. **Add SSH Key to GitHub Secrets**
   - Generate SSH key pair if you don't have one: `ssh-keygen -t rsa -b 4096`
   - Copy the public key to your server: `ssh-copy-id ubuntu@<server_ip>`
   - Add private key to GitHub:
     - Go to: Repository Settings → Secrets and variables → Actions
     - Click "New repository secret"
     - Name: `SSH_PRIVATE_KEY`
     - Value: Content of your private key file (e.g., `~/.ssh/id_rsa`)

2. **Run the Workflow**
   - Go to: Actions tab → Deploy Ansible Playbook
   - Click "Run workflow"
   - Enter your server IP address
   - Click "Run workflow"

## Local Usage

```bash
# Install dependencies
pip install ansible
ansible-galaxy collection install -r requirements.yml

# Edit inventory with your server IP
nano inventory.ini

# Run playbook
ansible-playbook playbook.yml
```

## What Gets Configured

- ✅ System updates and upgrades
- ✅ htop installed
- ✅ fail2ban installed and enabled
- ✅ SSH hardened (no password auth, no root login)
- ✅ nginx and apache2 removed
- ✅ UFW firewall configured:
  - Default deny incoming
  - Default allow outgoing
  - Allow SSH (port 22)

## Important Notes

⚠️ **BEFORE running this playbook:**
- Ensure you have SSH key-based access to the server
- Test SSH connection: `ssh ubuntu@<server_ip>`
- This playbook disables password authentication

⚠️ **After running:**
- SSH password authentication will be disabled
- Root login will be disabled
- Only SSH key-based authentication will work
- Make sure you can still access the server!

## Troubleshooting

**Can't connect after running playbook?**
- Check if your SSH key is properly configured
- Try: `ssh -i ~/.ssh/id_rsa ubuntu@<server_ip>`
- Check UFW status: `sudo ufw status`

**Playbook fails?**
- Run with verbose mode: `ansible-playbook playbook.yml -vvv`
- Check if you can sudo: `ssh ubuntu@<server_ip> sudo whoami`
- Ensure python3 is installed on target server

**Need to run specific tasks only?**
```bash
# Only update packages
ansible-playbook playbook.yml --tags update

# Only configure firewall
ansible-playbook playbook.yml --tags firewall

# Only setup SSH
ansible-playbook playbook.yml --tags ssh
```
