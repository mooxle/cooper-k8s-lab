# Proxmox VE Deployment Guide - Cooper'n'80s Lab

**Complete automation from hardware to SSH-accessible Proxmox nodes**

## Overview

This guide describes the complete automated deployment process for Proxmox VE nodes using Infrastructure as Code principles. The process takes a bare-metal Mini PC from power-on to a fully configured, SSH-accessible Proxmox node in approximately 45 minutes with minimal manual intervention.

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌───────────────────┐
│   Jumphost      │    │    Vault Server  │    │   DNS/DHCP Server │
│ (LXC Container) │    │  (192.168.1.23)  │    │  (192.168.1.23)   │
│ ansible.sammet  │◄──►│  Credential Mgmt │◄──►│   IP Reservations  │
│   .me:8015      │    │                  │    │                   │
└─────────────────┘    └──────────────────┘    └───────────────────┘
         │                                              │
         ▼                                              ▼
┌─────────────────────────────────────┐    ┌─────────────────────┐
│           Target Nodes              │    │    Network VLAN     │
│  Node-01: 10.0.1.10               │    │   10.0.1.0/24      │
│  Node-02: 10.0.1.11               │    │   Gateway: 10.0.1.1 │
│  Node-03: 10.0.1.12               │    │                     │
└─────────────────────────────────────┘    └─────────────────────┘
```

## Prerequisites

### Infrastructure Components
- **Jumphost**: LXC container with Ansible, Terraform, Vault CLI
- **Vault Server**: HashiCorp Vault for credential management
- **DNS/DHCP**: PowerDNS + Kea DHCP with IP reservations
- **Git Repository**: Infrastructure code versioning

### Hardware Requirements
- Intel-based Mini PC with NVMe SSD
- Network connectivity to lab VLAN
- USB port for installation media
- Monitor and keyboard for initial BIOS setup

## Complete Deployment Process

### Phase 1: Infrastructure Preparation (Jumphost)

#### 1.1 Vault Secret Generation
**Location**: `~/cooper-lab/terraform/environments/dev/`
**Script**: `main.tf`

```bash
cd ~/cooper-lab/terraform/environments/dev/
terraform plan
terraform apply
```

**Creates per node**:
- Secure 24-character root password
- ED25519 SSH key pair (private/public)
- Node metadata (hostname, IP address, creation date)
- Vault KV storage at `cooper-n-80s/environments/dev/nodes/{node-name}`

#### 1.2 Node-Specific ISO Creation
**Location**: `~/cooper-lab/operations/proxmox/`
**Script**: `create-node-iso.sh`

```bash
./create-node-iso.sh cooper-node-01
```

**Process**:
- Retrieves credentials from Vault
- Generates TOML answer file with node-specific configuration
- Uses `proxmox-auto-install-assistant` to create auto-install ISO
- Output: `proxmox-ve_8.4-1-{node-name}-autoinstall.iso`

**Answer File Contents**:
- German keyboard layout and timezone (Europe/Berlin)
- DHCP network configuration
- ext4 filesystem on nvme0n1
- Automated installation parameters

### Phase 2: Installation Media Creation (macOS)

#### 2.1 ISO Transfer
```bash
scp maxsammet@ansible.sammet.me:~/cooper-lab/tmp/proxmox-prep/proxmox-ve_8.4-1-{node-name}-autoinstall.iso ~/Downloads/
```

#### 2.2 USB Creation
```bash
# Identify USB device
diskutil list external

# Create bootable USB
sudo diskutil unmountDisk force /dev/disk4
sudo dd if=~/Downloads/proxmox-ve_8.4-1-{node-name}-autoinstall.iso of=/dev/rdisk4 bs=1m status=progress
sync
diskutil eject /dev/disk4
```

### Phase 3: Hardware Installation

#### 3.1 Physical Setup
1. Connect Mini PC to lab network (switch port)
2. Insert USB installation media
3. Connect monitor and keyboard temporarily
4. Power on the system

#### 3.2 BIOS Configuration ⚠️ **CRITICAL**
**Access**: F2, F11, F12, or DEL during boot (hardware dependent)

**Required Settings**:
```
Storage Configuration:
├── SATA Controller: [ENABLED]
├── SATA Mode: [AHCI] ← MUST change from "Intel RST" or "RAID"
├── M.2 Configuration: [ENABLED]
└── NVMe Support: [ENABLED]

Security:
├── Secure Boot: [DISABLED]
└── VT-x/VT-d: [ENABLED]

Boot:
├── USB Boot: [ENABLED]
├── Boot Mode: [UEFI] (not Legacy)
└── Boot Priority: [USB First]
```

**⚠️ CRITICAL**: Intel RST → AHCI Mode switch is essential. Without this, NVMe drives are invisible to the Proxmox installer.

#### 3.3 Automated Installation
1. Boot from USB (automatic after BIOS save)
2. Proxmox boot menu appears with "Automated Installation" option
3. 10-second timeout triggers automatic installation
4. Complete installation process: ~15-20 minutes
5. System reboots automatically
6. Node becomes available at assigned IP address

**Installation validates**:
- Network connectivity via DHCP
- DNS registration (node-name.cooper.lab)
- Answer file processing
- Automatic disk partitioning and OS installation

### Phase 4: Post-Installation Automation (Jumphost)

#### 4.1 Ansible Inventory Configuration
**Location**: `~/cooper-lab/ansible/inventory/proxmox.yml`

**Structure for fresh node** (pre-post-deploy):
```yaml
cooper-node-XX:
  ansible_host: 10.0.1.1X
  ansible_user: root
  vault_node_path: cooper-n-80s/environments/dev/nodes/cooper-node-XX
  # Uses password auth initially
```

#### 4.2 Post-Deploy Execution
**Location**: `~/cooper-lab/operations/proxmox/`
**Script**: `post-deploy-setup.sh`

```bash
./post-deploy-setup.sh cooper-node-01
```

**Automation Process**:
1. **Credential Retrieval**: Fetches node secrets from Vault via Ansible URI module
2. **Connection Management**: Tests SSH key auth first, falls back to password auth for fresh installs
3. **SSH Key Deployment**: Installs public key from Vault to `~/.ssh/authorized_keys`
4. **System Hardening**: Configures fail2ban, SSH daemon settings, disables password auth
5. **Package Management**: Updates system, installs essential tools
6. **Repository Configuration**: Replaces enterprise repos with no-subscription variants
7. **Network Configuration**: Sets up VM bridge (vmbr1: 10.0.10.0/24)
8. **Monitoring Setup**: Configures Prometheus node-exporter on port 9100
9. **Backup Configuration**: Sets up automated daily backups at 02:30
10. **Service Validation**: Verifies all components are operational

**Playbook**: `~/cooper-lab/ansible/playbooks/proxmox-complete-setup.yml`
- 60+ Ansible tasks
- Vault-integrated credential management
- Idempotent operations
- Error handling and rollback capabilities

#### 4.3 Post-Deploy Inventory Update
**Updates inventory to SSH key authentication**:
```yaml
cooper-node-XX:
  ansible_host: 10.0.1.1X
  ansible_user: root
  vault_node_path: cooper-n-80s/environments/dev/nodes/cooper-node-XX
  ansible_ssh_private_key_file: ~/.ssh/cooper-node-XX_key
```

### Phase 5: Validation and Testing

#### 5.1 SSH Key Setup (Jumphost)
```bash
# Extract SSH private key from Vault
vault kv get -format=json cooper-n-80s/environments/dev/nodes/cooper-node-XX | jq -r '.data.data.ssh_private_key' > ~/.ssh/cooper-node-XX_key
chmod 600 ~/.ssh/cooper-node-XX_key
```

#### 5.2 Connectivity Validation
```bash
cd ~/cooper-lab/ansible

# Test individual node
ansible cooper-node-XX -m ping

# System information
ansible cooper-node-XX -m command -a "hostname -f && systemctl is-active fail2ban prometheus-node-exporter"

# Proxmox version
ansible cooper-node-XX -m command -a "pveversion"
```

#### 5.3 Service Verification
```bash
# Web interface test
curl -k https://10.0.1.1X:8006

# Monitoring endpoint
curl http://10.0.1.1X:9100/metrics

# SSH direct access
ssh -i ~/.ssh/cooper-node-XX_key root@10.0.1.1X
```

## Tools and Scripts Reference

### Jumphost Tools
| Tool | Purpose | Location |
|------|---------|----------|
| Terraform | Infrastructure provisioning | `~/cooper-lab/terraform/environments/dev/main.tf` |
| Vault CLI | Secret management | System installed |
| Ansible | Configuration management | `~/cooper-lab/ansible/` |
| proxmox-auto-install-assistant | ISO preparation | System installed |

### Custom Scripts
| Script | Purpose | Location |
|--------|---------|----------|
| `create-node-iso.sh` | Node-specific ISO creation | `~/cooper-lab/operations/proxmox/` |
| `post-deploy-setup.sh` | Ansible automation wrapper | `~/cooper-lab/operations/proxmox/` |
| `main.tf` | Terraform infrastructure | `~/cooper-lab/terraform/environments/dev/` |

### Configuration Files
| File | Purpose | Location |
|------|---------|----------|
| `proxmox.yml` | Ansible inventory | `~/cooper-lab/ansible/inventory/` |
| `proxmox-complete-setup.yml` | Main playbook | `~/cooper-lab/ansible/playbooks/` |
| `ansible.cfg` | Ansible configuration | `~/cooper-lab/ansible/` |
| `answer.toml.template` | Installation template | `~/cooper-lab/templates/proxmox/` |

## Security Implementation

### Credential Management
- **No hardcoded passwords**: All credentials stored in Vault
- **Dynamic generation**: Unique 24-character passwords per node
- **SSH key rotation**: Fresh ED25519 keys per deployment
- **Vault integration**: Ansible retrieves credentials at runtime

### Network Security
- **SSH key-only authentication**: Passwords disabled post-deployment
- **fail2ban protection**: Automatic IP banning for brute force attempts
- **Network isolation**: Dedicated VLAN (10.0.1.0/24)
- **VM network separation**: Isolated bridge (10.0.10.0/24)

### System Hardening
- **Minimal attack surface**: Only essential services enabled
- **Regular updates**: Automated package management
- **Service monitoring**: Prometheus metrics collection
- **Backup automation**: Daily backup retention

## Troubleshooting Common Issues

### BIOS Configuration Problems
**Symptom**: "No supported hard disks found"
**Solution**: Verify Intel RST → AHCI mode switch in BIOS

### Network Connectivity Issues
**Symptom**: Node not reachable after installation
**Solution**: Check DHCP reservation and DNS registration

### SSH Authentication Failures
**Symptom**: Permission denied despite correct credentials
**Solution**: Verify host key acceptance and SSH key permissions (600)

### Ansible Connection Timeouts
**Symptom**: Connection hangs during post-deploy
**Solution**: Ensure sshpass is installed and password authentication works

## Deployment Timeline

| Phase | Duration | Manual Effort |
|-------|----------|---------------|
| Infrastructure prep | 2 minutes | Minimal (script execution) |
| ISO creation | 2 minutes | Minimal (script execution) |
| USB creation | 5 minutes | Low (macOS commands) |
| BIOS setup | 5 minutes | High (physical access required) |
| Installation | 20 minutes | None (automated) |
| Post-deploy | 10 minutes | Minimal (script execution) |
| **Total** | **~45 minutes** | **Mostly automated** |

## Multi-Node Scaling

The process is identical for each node:

```bash
# Node-02 deployment
./create-node-iso.sh cooper-node-02
# [Transfer, USB creation, hardware installation]
./post-deploy-setup.sh cooper-node-02

# Node-03 deployment  
./create-node-iso.sh cooper-node-03
# [Transfer, USB creation, hardware installation]
./post-deploy-setup.sh cooper-node-03
```

**Parallel deployment possible**: Multiple nodes can be processed simultaneously as each has unique credentials and configuration.

## Success Criteria

A successful deployment achieves:

✅ **Automated installation**: Zero manual intervention during OS installation
✅ **Network integration**: Automatic IP assignment and DNS registration
✅ **Security configuration**: SSH key-only access with hardening applied
✅ **Service availability**: Web interface accessible at https://10.0.1.1X:8006
✅ **Ansible management**: Node responds to `ansible node-name -m ping`
✅ **Monitoring**: Prometheus metrics available at http://10.0.1.1X:9100
✅ **Backup readiness**: Automated backup scheduled and tested

## Advanced Features

### Infrastructure as Code Benefits
- **Version control**: All configuration tracked in Git
- **Reproducibility**: Identical deployments guaranteed
- **Rollback capability**: Previous configurations recoverable
- **Documentation**: Self-documenting infrastructure

### Vault Integration
- **Dynamic secrets**: Credentials generated per deployment
- **Audit trail**: All secret access logged
- **Role-based access**: Service accounts for automation
- **Secret rotation**: Capability for credential updates

### Monitoring and Observability
- **System metrics**: CPU, memory, disk, network statistics
- **Service health**: Proxmox service status monitoring
- **Performance tracking**: Historical data collection
- **Alert capability**: Integration-ready for notification systems

## Conclusion

This deployment process represents enterprise-grade infrastructure automation applied to homelab infrastructure. The combination of HashiCorp Vault, Terraform, Ansible, and official Proxmox tools creates a robust, secure, and highly repeatable deployment pipeline that transforms bare metal hardware into production-ready virtualization nodes with minimal manual intervention.

The scientific methodology of hypothesis → experiment → documentation ensures reliable, reproducible results suitable for both learning environments and production infrastructure needs.

---

**Result**: From powered-off Mini PC to SSH-accessible, fully configured Proxmox VE node in under 45 minutes with enterprise-grade security and monitoring.
