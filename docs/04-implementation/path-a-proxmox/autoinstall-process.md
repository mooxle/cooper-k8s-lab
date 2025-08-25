# Proxmox VE Auto-Install with proxmox-auto-install-assistant

**Cooper'n'80s Kubernetes Lab - Automated Proxmox Installation**

## Overview

This guide uses the official Proxmox `proxmox-auto-install-assistant` tool to create Auto-Install ISOs with embedded answer files. This method is more elegant and reliable than manual USB partitioning approaches.

## Prerequisites

### Jumphost (LXC Container)
- Debian/Ubuntu based system
- Vault access configured
- Internet access for package installation

### Local Computer (macOS)
- SSH access to jumphost
- USB port for installation media

## Tool Installation (Jumphost)

### 1. Add Proxmox Repository

```bash
# On the jumphost (ansible.sammet.me)
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bookworm pve-no-subscription" | \
sudo tee /etc/apt/sources.list.d/pve-install-repo.list

# Add GPG key
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /tmp/proxmox-release-bookworm.gpg
sudo cp /tmp/proxmox-release-bookworm.gpg /etc/apt/trusted.gpg.d/

# Update repository
sudo apt update
```

### 2. Install Tool

```bash
# Install Proxmox Auto-Install Assistant and dependencies
sudo apt install proxmox-auto-install-assistant xorriso

# Verify installation
proxmox-auto-install-assistant --help
```

## Node Installation Process

### 1. Prepare ISO and Answer File (Jumphost)

```bash
# Working directory
cd ~/cooper-lab/tmp/proxmox-prep

# Download Proxmox ISO (if not present)
wget -O proxmox-ve_8.4-1.iso https://enterprise.proxmox.com/iso/proxmox-ve_8.4-1.iso

# Create answer file from Vault secrets
~/cooper-lab/operations/proxmox/prepare-installation.sh
```

### 2. Create Auto-Install ISO

```bash
# Create ISO with embedded answer file
proxmox-auto-install-assistant prepare-iso \
    proxmox-ve_8.4-1.iso \
    --fetch-from iso \
    --answer-file answer.toml \
    --output proxmox-ve_8.4-1-autoinstall.iso

# Success: "Final ISO is available at proxmox-ve_8.4-1-autoinstall.iso"
```

### 3. Answer File Format (TOML)

```toml
[global]
keyboard = "de"
country = "de"
fqdn = "cooper-node-01.cooper.lab"
mailto = "admin@cooper.lab"
timezone = "Europe/Berlin"
root-password = "SECURE_PASSWORD_FROM_VAULT"

[network]
source = "from-dhcp"

[disk-setup]
filesystem = "ext4"
disk-list = ["nvme0n1"]
```

### 4. Create USB Media (macOS)

```bash
# Transfer auto-install ISO to local computer
scp maxsammet@ansible.sammet.me:~/cooper-lab/tmp/proxmox-prep/proxmox-ve_8.4-1-autoinstall.iso ~/Downloads/

# Identify USB device
diskutil list external

# Create USB (WARNING: DELETES ALL DATA!)
sudo diskutil unmountDisk force /dev/disk4
sudo dd if=~/Downloads/proxmox-ve_8.4-1-autoinstall.iso of=/dev/rdisk4 bs=1m status=progress
sync
diskutil eject /dev/disk4
```

## Installation on Node

### 1. Hardware Preparation

- Insert USB stick into node
- Connect monitor and keyboard
- Power on the system

### 2. BIOS Configuration

**Access Boot Menu:** F2, F11, F12 or DEL (hardware dependent)

**⚠️ CRITICAL - Storage Controller Settings:**
- ✅ **SATA Mode: AHCI** (NOT Intel RST/RAID!)
- ✅ **NVMe Support: ENABLED**
- ✅ **M.2 Slot: ENABLED**

**Additional Required Settings:**
- ✅ Intel VT-x/VT-d: **ENABLED**
- ✅ Secure Boot: **DISABLED**
- ✅ Legacy Boot: **DISABLED** (UEFI only)
- ✅ USB Boot: **ENABLED**
- ✅ Boot Order: **USB First**

**🚨 Intel RST Issue on Windows 11 Systems:**

If internal NVMe/SSD not detected (only USB device `sda` visible):

1. **Windows 11 often enables Intel RST** (Rapid Storage Technology)
2. **RST mode hides NVMe** from Linux systems
3. **Solution:** Switch SATA Mode from `Intel RST` to `AHCI`
4. **⚠️ Important:** This makes Windows temporarily unbootable - Windows Safe Mode boot required

**BIOS Storage Settings Example:**
```
Storage Configuration:
  SATA Controller: [ENABLED]
  SATA Mode: [AHCI]          ← NOT "Intel RST" or "RAID"
  M.2_1 Configuration: [ENABLED]
  NVMe Support: [ENABLED]
```

### 3. Automated Installation

1. **Boot from USB** → Proxmox boot menu appears
2. **"Automated Installation"** is displayed
3. **Automatic start** after 10-second timeout
4. **Installation runs** (~15-20 minutes)
5. **Automatic reboot** after completion

### 4. Post-Installation Access

**Web Interface:**
```
URL: https://NODE_IP:8006
Username: root
Password: [from Vault]
```

**SSH Access:**
```bash
ssh root@NODE_IP
```

**IP Address:** Automatically assigned via DHCP reservation

## Vault Integration

### Retrieve Password

```bash
# On the jumphost
vault kv get -field=root_password cooper-n-80s/environments/dev/nodes/cooper-node-01
```

### For Additional Nodes

```bash
# Create new node secrets in Vault
terraform apply

# Generate answer file for new node
# (adapt prepare-installation.sh for other node names)

# Create auto-install ISO for new node
proxmox-auto-install-assistant prepare-iso \
    proxmox-ve_8.4-1.iso \
    --fetch-from iso \
    --answer-file answer-nodeXX.toml \
    --output proxmox-ve_8.4-1-nodeXX-autoinstall.iso
```

## Advantages of This Method

- ✅ **Official Proxmox Tool** - no custom scripts
- ✅ **Embedded Answer Files** - no separate HTTP server
- ✅ **Bootable ISOs** - no USB partitioning
- ✅ **10s Auto-Start** - minimal user interaction
- ✅ **Complete Automation** - from boot to login
- ✅ **Parameterizable** - same process for all nodes

## Troubleshooting

### Hardware Detection Issues

**Problem:** `ERROR: The installer could not find any supported hard disks`
**Cause:** NVMe/SSD not detected

**Solution:**
```bash
# 1. Check in Proxmox installer shell
lsblk -d
cat /proc/partitions
lspci | grep -i storage

# 2. Only USB device (sda) visible?
# → BIOS: Switch Intel RST to AHCI mode

# 3. Force hardware detection
modprobe nvme
lsblk
```

**Intel RST → AHCI Conversion:**
1. Reboot to BIOS/UEFI
2. Storage/SATA Configuration
3. SATA Mode: `Intel RST` → `AHCI` 
4. Save & Exit
5. Repeat Proxmox installation

### ISO Boot Issues

```bash
# Check UEFI/Legacy boot mode
# Completely disable Secure Boot
# Try USB in different port
```

### Answer File Validation

```bash
proxmox-auto-install-assistant validate-answer answer.toml
```

### Installation Logs

**During Installation:**
- TTY2: `Ctrl+Alt+F2` for logs
- TTY1: `Ctrl+Alt+F1` back to installation

**Post Installation:**
- Web Interface: System → Syslog
- SSH: `journalctl -f`

## Cleanup

### Remove Old Scripts

```bash
# These scripts are no longer needed:
rm ~/cooper-lab/operations/proxmox/prepare-installation.sh.old
rm ~/cooper-lab/scripts/macos/create-proxmox-usb.sh
rm -rf ~/cooper-lab/operations/answer-server
```

### Clean Git Repository

```bash
cd ~/cooper-lab
git add -A
git commit -m "Switch to proxmox-auto-install-assistant for Node installations

- Remove manual USB partitioning scripts
- Remove HTTP answer file server
- Add official Proxmox auto-install workflow
- Tested and working for automated Node-01 installation"

git push origin main
```

## Lessons Learned

### Cooper'n'80s Lab - Node-01 Installation

**Success Factors:**
- ✅ **proxmox-auto-install-assistant:** Official tool works perfectly
- ✅ **TOML Answer Files:** Much more robust than old config syntax
- ✅ **Vault Integration:** Secure password management seamlessly integrated
- ✅ **DHCP/DNS Automation:** IP reservation works as planned

**Critical Hardware Insights:**
- 🚨 **Intel RST vs AHCI:** Most common stumbling block on Windows 11 systems
- 🔧 **BIOS Configuration:** Storage controller settings are critical
- 💿 **Disk Detection:** `lsblk` and `cat /proc/partitions` for hardware debug
- 🖥️ **UEFI Mode:** Modern systems require pure UEFI boot (no Legacy)

**Automation Success:**
- ⚡ **10-Second Auto-Start:** Minimal user interaction
- 🔄 **Reproducible:** Identical process for Node-02/03
- 📋 **Parameterized:** Answer files generated via Vault secrets
- 🎯 **End-to-End:** Boot to login completely automated

### Next Steps

**Node-02/03 Preparation:**
1. Create Vault secrets for additional nodes
2. Generate answer files with correct hostnames  
3. Create auto-install ISOs for each node
4. Document hardware-specific BIOS configuration

**Post-Install Automation:**
1. Ansible playbooks for SSH keys, SSL certs
2. Storage pool and network bridge configuration
3. Monitoring and backup integration
4. Prepare cluster formation (later)