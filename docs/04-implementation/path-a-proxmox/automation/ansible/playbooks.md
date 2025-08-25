# Ansible Playbooks Collection

> Enterprise-grade automation playbooks for Cooper'n'80s Proxmox infrastructure

## 🎯 Overview

Comprehensive collection of Ansible playbooks for complete lifecycle management of Proxmox nodes, from initial deployment through ongoing maintenance and power management.

## 📋 Playbook Inventory

| Playbook | Purpose | Usage | Status |
|----------|---------|-------|--------|
| **[proxmox-complete-setup.yml](#complete-post-deploy-setup)** | Complete node configuration | Post-installation | ✅ Production |
| **[power-control.yml](#power-management)** | WOL startup/shutdown | Operations | ✅ Production |
| **[create_user.yml](#user-management)** | User account management | Administration | ✅ Production |
| **[run_maintenance.yml](#maintenance-operations)** | System maintenance | Operations | ✅ Production |

## 🚀 Complete Post-Deploy Setup

**File**: `ansible/playbooks/proxmox-complete-setup.yml`

### Purpose
Complete automation of Proxmox node configuration from fresh installation to production-ready state.

### Features
- ✅ **Vault Integration**: Dynamic credential retrieval
- ✅ **SSH Key Deployment**: Automated key-based authentication
- ✅ **Security Hardening**: fail2ban, SSH configuration, system hardening
- ✅ **Repository Management**: No-subscription repos, package updates
- ✅ **Network Configuration**: VM bridges, DHCP normalization
- ✅ **Monitoring Setup**: Prometheus node-exporter integration
- ✅ **Backup Configuration**: Automated daily backup scheduling

### Usage
```bash
# Deploy specific node
ansible-playbook -i inventory/proxmox.yml playbooks/proxmox-complete-setup.yml --limit cooper-node-01

# Deploy all nodes
ansible-playbook -i inventory/proxmox.yml playbooks/proxmox-complete-setup.yml

# Check what would be changed
ansible-playbook -i inventory/proxmox.yml playbooks/proxmox-complete-setup.yml --check
```

### Automation Phases

#### Phase 1: Credential Management
- Retrieves node-specific secrets from Vault
- Sets up temporary SSH key authentication
- Validates Vault connectivity and credentials

#### Phase 2: SSH Key Deployment
- Tests password authentication for fresh installs
- Deploys SSH public keys to authorized_keys
- Switches to key-based authentication

#### Phase 3: Repository & Updates
- Configures Proxmox no-subscription repositories
- Disables enterprise repositories
- Updates all system packages

#### Phase 4: Security Hardening
- Configures SSH daemon for security
- Deploys fail2ban with SSH protection
- Disables password authentication

#### Phase 5: Network Configuration
- Normalizes vmbr0 to pure DHCP
- Configures VM bridge (vmbr1) with per-host IP
- Forces DHCP renewal for DNS registration

#### Phase 6: Proxmox Configuration
- Updates Proxmox certificates
- Configures storage directories
- Adds local storage pools

#### Phase 7: Monitoring Setup
- Configures Prometheus node-exporter
- Sets up system metrics collection
- Enables service monitoring

#### Phase 8: Backup Automation
- Deploys automated backup scripts
- Schedules daily backups at 02:30
- Configures 7-day retention policy

#### Phase 9: Validation
- Verifies all services are running
- Displays setup completion summary
- Provides next-step guidance

### Key Variables
```yaml
vault_addr: "http://vault.server:8200"          # Vault server URL
vault_token: "{{ lookup('env', 'VAULT_TOKEN') }}"  # Authentication token
vmbr1_ip: "{{ vmbr1_ip }}"                      # Per-host VM bridge IP
```

## ⚡ Power Management

**File**: `ansible/playbooks/power-control.yml`

### Purpose
Remote power management using Wake-on-LAN for startup and clean shutdown for power-down.

### Features
- ✅ **Wake-on-LAN**: Network-based startup
- ✅ **Clean Shutdown**: Systemctl poweroff
- ✅ **Connection Waiting**: Optional SSH ready detection
- ✅ **Broadcast Control**: Configurable WOL parameters

### Usage
```bash
# Shutdown all nodes
ansible-playbook -i inventory/proxmox.yml playbooks/power-control.yml -e "power_action=down"

# Start specific node and wait for SSH
ansible-playbook -i inventory/proxmox.yml playbooks/power-control.yml \
  --limit cooper-node-01 \
  -e "power_action=up power_wait_after=true power_wait_timeout=600"

# Shutdown specific node
ansible-playbook -i inventory/proxmox.yml playbooks/power-control.yml \
  --limit cooper-node-02 \
  -e "power_action=down"
```

### Variables
```yaml
power_action: "up|down"           # Required: startup or shutdown
power_wait_after: true            # Optional: wait for SSH after startup
power_wait_timeout: 600           # Timeout for SSH availability (seconds)
wol_broadcast: "10.0.1.255"       # WOL broadcast address
wol_port: 9                       # WOL UDP port
```

### Requirements
- **MAC Addresses**: Must be configured in inventory (`wol_mac`)
- **Wake-on-LAN**: Must be enabled in BIOS/UEFI
- **Network Access**: Jumphost must reach broadcast network

## 👤 User Management

**File**: `ansible/playbooks/create_user.yml`

### Purpose
Create and manage local user accounts with SSH key authentication and optional sudo access.

### Features
- ✅ **User Creation**: Local account with home directory
- ✅ **SSH Key Deployment**: Public key authentication
- ✅ **Sudo Configuration**: Optional passwordless sudo
- ✅ **Password Management**: Secure password hashing
- ✅ **Validation**: Ensures required parameters

### Usage
```bash
# Create standard user with SSH key
ansible-playbook -i inventory/proxmox.yml playbooks/create_user.yml \
  -e "username=deploy pubkey_path=~/.ssh/deploy.pub"

# Create superuser with sudo access
ansible-playbook -i inventory/proxmox.yml playbooks/create_user.yml \
  -e "username=admin pubkey_path=~/.ssh/admin.pub superuser=true"

# Create user with password
ansible-playbook -i inventory/proxmox.yml playbooks/create_user.yml \
  -e "username=service password=SecurePassword123"
```

### Variables
```yaml
username: "deploy"                    # Required: username to create
pubkey_path: "~/.ssh/user.pub"       # Optional: SSH public key file
password: "SecurePassword"            # Optional: user password
superuser: false                      # Optional: grant sudo access
user_shell: "/bin/bash"              # Optional: default shell
```

### Security Features
- **Password Hashing**: SHA512 with salt
- **SSH Key Management**: Automated authorized_keys deployment
- **Sudo Configuration**: Secure passwordless sudo when needed
- **Validation**: Ensures sudo package and visudo availability

## 🔧 Maintenance Operations

**File**: `ansible/playbooks/run_maintenance.yml`

### Purpose
On-demand system maintenance including updates, cleanup, health checks, and optional automated reboots.

### Features
- ✅ **System Updates**: Proxmox and Debian package updates
- ✅ **Cleanup Operations**: Journal vacuum, package cleanup
- ✅ **Health Checks**: SMART disk health, ZFS scrub
- ✅ **Smart Rebooting**: Conditional reboot with safety checks
- ✅ **Maintenance Windows**: Time-based reboot control

### Usage
```bash
# Basic maintenance (no reboot)
ansible-playbook -i inventory/proxmox.yml playbooks/run_maintenance.yml

# Maintenance with auto-reboot if needed
ansible-playbook -i inventory/proxmox.yml playbooks/run_maintenance.yml \
  -e "auto_reboot=true"

# Maintenance with ZFS scrub
ansible-playbook -i inventory/proxmox.yml playbooks/run_maintenance.yml \
  -e "run_zfs_scrub=true zfs_scrub_pools='rpool tank'"

# Specific node maintenance
ansible-playbook -i inventory/proxmox.yml playbooks/run_maintenance.yml \
  --limit cooper-node-01 \
  -e "auto_reboot=true"
```

### Variables
```yaml
# Maintenance Control
install_optional_tools: true         # Install smartmontools, zfsutils
journal_keep: "14d"                  # Journal retention period
run_smart_health: true              # SMART health checks
run_zfs_scrub: false                 # ZFS scrub operations
zfs_scrub_pools: ""                  # Specific pools or auto-detect

# Reboot Control
auto_reboot: false                   # Enable conditional auto-reboot
reboot_window_start: "03:00"         # Earliest reboot time
reboot_window_end: "05:00"           # Latest reboot time
```

### Reboot Safety Conditions
The playbook will only automatically reboot if ALL conditions are met:
- ✅ `auto_reboot=true` specified
- ✅ Reboot is actually required (kernel update or /var/run/reboot-required)
- ✅ Current time within maintenance window
- ✅ No VMs/containers running
- ✅ Cluster quorum maintained (if applicable)

### Maintenance Operations
1. **Package Updates**: `pveupdate`, `apt full-upgrade`
2. **Cleanup**: `apt autoremove`, `apt autoclean`
3. **Journal Management**: `journalctl --vacuum-time`
4. **SMART Health**: Health check on all storage devices
5. **ZFS Scrub**: Optional pool scrubbing
6. **Conditional Reboot**: Smart reboot with safety checks

## 🔧 Execution Patterns

### Sequential Node Processing
```bash
# Process nodes one at a time
ansible-playbook -i inventory/proxmox.yml playbooks/run_maintenance.yml \
  --serial 1 \
  -e "auto_reboot=true"
```

### Parallel Operations (Safe Playbooks)
```bash
# Safe for parallel execution
ansible-playbook -i inventory/proxmox.yml playbooks/power-control.yml \
  --forks 3 \
  -e "power_action=up"
```

### Environment-Specific Variables
```bash
# Override defaults with extra vars
ansible-playbook -i inventory/proxmox.yml playbooks/proxmox-complete-setup.yml \
  -e "vault_addr=https://vault.company.com:8200"
```

## 📊 Logging and Monitoring

### Execution Logs
All playbooks include comprehensive logging:
- **Maintenance Log**: `/var/log/cooper-maintenance/daily.log`
- **Ansible Logs**: Controller-side execution logs
- **System Integration**: Systemd journal integration

### Health Monitoring
- **Service Status**: Systemd service verification
- **Resource Usage**: System resource monitoring
- **Performance Metrics**: Prometheus integration ready

## 🛡️ Security Considerations

### Credential Management
- **No Hardcoded Secrets**: All credentials via Vault or environment
- **SSH Key Rotation**: Support for automated key updates
- **Audit Trail**: All operations logged

### Network Security
- **SSH-Only Access**: No password authentication
- **Connection Isolation**: Custom SSH arguments for lab security
- **Network Segmentation**: VLAN isolation maintained

### System Hardening
- **Minimal Privileges**: Operations use least-privilege principle
- **Service Validation**: All changes validated before application
- **Rollback Capability**: Backup configurations before changes

---

**Status**: ✅ **PRODUCTION-READY** - All playbooks tested and operational  
**Integration**: Full Vault and Cooper'n'80s lab infrastructure integration  
**Maintenance**: Regular updates aligned with infrastructure evolution