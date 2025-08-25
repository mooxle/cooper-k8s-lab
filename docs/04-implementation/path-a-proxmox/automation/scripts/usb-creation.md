# USB Creation Script for macOS

> Automated creation of Proxmox auto-install USB media on macOS

## 🎯 Overview

The `create-proxmox-usb.sh` script automates the creation of bootable USB installation media for Proxmox VE with embedded answer files, designed specifically for macOS systems.

## 📋 Script Features

### Core Capabilities
- ✅ **Dual-Partition Layout**: Bootable ISO + Answer file storage
- ✅ **Safety Checks**: Device validation and confirmation prompts
- ✅ **File Verification**: Ensures required files are present
- ✅ **Performance Optimized**: Uses raw device access for faster writes
- ✅ **Error Handling**: Comprehensive error checking and recovery

### Cooper'n'80s Integration
- ✅ **Vault Integration**: Works with Vault-generated answer files
- ✅ **Node-Specific**: Supports per-node configuration
- ✅ **Network Ready**: Pre-configured for cooper.lab domain
- ✅ **Automation Friendly**: Part of complete deployment pipeline

## 🔧 Script Location and Usage

### File Location
```bash
~/cooper-lab/scripts/macos/create-proxmox-usb.sh
```

### Prerequisites
- **macOS System**: Script designed for macOS diskutil commands
- **Administrator Access**: Requires sudo for disk operations
- **Required Files**: Proxmox ISO and answer file in Downloads folder

### Required Files Setup
```bash
# Transfer files from jumphost to macOS Downloads
scp maxsammet@ansible.sammet.me:~/cooper-lab/tmp/proxmox-prep/proxmox-ve_8.4-1.iso ~/Downloads/
scp maxsammet@ansible.sammet.me:~/cooper-lab/tmp/proxmox-prep/answer.toml ~/Downloads/
```

### Basic Usage
```bash
cd ~/cooper-lab/scripts/macos
chmod +x create-proxmox-usb.sh
./create-proxmox-usb.sh
```

## 🎮 Interactive Process

### Step 1: File Verification
The script checks for required files in ~/Downloads:
- `proxmox-ve_8.4-1.iso` (Proxmox installation ISO)
- `answer.toml` (Node-specific answer file)

### Step 2: USB Device Selection
```bash
# Script displays available USB devices
diskutil list external physical

# User selects device (e.g., disk4)
Enter USB device identifier (e.g., disk4): disk4
```

### Step 3: Safety Confirmation
- Device information displayed for verification
- Explicit confirmation required before proceeding
- Warning about complete data erasure

### Step 4: Partition Creation
```bash
# Creates dual-partition layout
Partition 1: EFI (automatic by macOS)
Partition 2: PROXMOX (1.6GB) - Bootable ISO
Partition 3: PROXMOX-AIS (remaining) - Answer file
```

### Step 5: Data Writing
- ISO written to bootable partition using raw device for speed
- Answer file copied to data partition
- File system synchronization
- Content verification

### Step 6: Safe Ejection
- Automatic USB ejection
- Success confirmation
- Next steps guidance

## 🔧 Technical Implementation

### Partition Strategy
```bash
# Dual-partition approach that works reliably
diskutil partitionDisk "/dev/$USB_DEVICE" 2 MS-DOS PROXMOX 1.6G MS-DOS PROXMOX-AIS R
```

### Performance Optimization
```bash
# Raw device access for better performance
sudo dd if="$ISO_FILE" of="/dev/r${USB_DEVICE}s2" bs=1m status=progress
```

### Mount Management
```bash
# Force unmount to prevent "Resource busy" errors
diskutil unmountDisk "/dev/$USB_DEVICE" >/dev/null 2>&1 || true

# Selective partition mounting
sudo diskutil mount force "/dev/${USB_DEVICE}s3"
```

## 🛡️ Safety Features

### Device Validation
- Validates device identifier format (diskX)
- Confirms device existence before proceeding
- Displays device information for verification

### User Confirmations
- Explicit confirmation required for destructive operations
- Clear warnings about data loss
- Option to cancel at any point

### Error Recovery
- Comprehensive error handling
- Fallback mounting strategies
- Manual recovery instructions when needed

## 📋 Configuration Template

### Answer File Format (TOML)
The script works with TOML answer files:
```toml
[global]
keyboard = "de"
country = "de" 
fqdn = "cooper-node-01.cooper.lab"
mailto = "admin@cooper.lab"
timezone = "Europe/Berlin"
root-password = "VAULT_GENERATED_PASSWORD"

[network]
source = "from-dhcp"

[disk-setup]
filesystem = "ext4"
disk-list = ["nvme0n1"]
```

## 🚀 Integration with Automation Pipeline

### Vault Integration
```bash
# Password retrieved from Vault during answer file creation
vault kv get -field=root_password cooper-n-80s/environments/dev/nodes/cooper-node-01
```

### Node-Specific Configuration
- Hostnames: `cooper-node-01.cooper.lab`, `cooper-node-02.cooper.lab`, etc.
- IP Addresses: Assigned via DHCP reservations
- Network: Automatic registration in cooper.lab domain

### Complete Workflow
1. **Terraform**: Generate Vault secrets per node
2. **Jumphost**: Create node-specific answer files and ISOs  
3. **macOS**: Create bootable USB with this script
4. **Hardware**: Boot from USB for automated installation
5. **Ansible**: Post-installation configuration and hardening

## 🎯 Output and Verification

### Success Output
```bash
🎉 Proxmox Auto-Install USB Creation Complete!
==================================================
✅ Proxmox VE 8.4 ISO written to bootable partition
✅ TOML answer file copied to PROXMOX-AIS partition
✅ USB configured for automated installation
✅ USB safely ejected

📋 Installation Configuration:
   • German keyboard layout (DE)
   • German country (DE) 
   • Timezone: Europe/Berlin
   • Hostname: cooper-node-01.cooper.lab
   • Network: DHCP (IP: 10.0.1.10 via reservation)
   • Storage: ext4 on nvme0n1
```

### Next Steps Guidance
```bash
🚀 Next Steps:
1. Take USB stick to Node-01
2. Connect monitor/keyboard to Node-01
3. Power on and enter BIOS (F2/DEL during boot)
4. BIOS Settings:
   - Enable VT-x/VT-d virtualization
   - Disable Secure Boot
   - Set USB as first boot device
5. Save BIOS and reboot from USB
6. Proxmox installer should auto-detect answer file
7. Installation runs automatically (~15-20 minutes)
8. Node reboots and is accessible at:
   - Web: https://10.0.1.10:8006
   - SSH: ssh root@10.0.1.10
```

## 🔍 Troubleshooting

### Common Issues and Solutions

#### "Resource busy" Errors
```bash
# Solution: Force unmount before operations
diskutil unmountDisk "/dev/$USB_DEVICE" >/dev/null 2>&1 || true
sleep 2
```

#### Partition Not Mounting
```bash
# Try different partition numbers
for partition in s1 s3; do
    sudo diskutil mount "/dev/${USB_DEVICE}${partition}"
done
```

#### Device Not Found
```bash
# Verify device with diskutil
diskutil list external
diskutil info "/dev/$USB_DEVICE"
```

### Manual Recovery
If automatic mounting fails, manual steps provided:
```bash
diskutil list /dev/$USB_DEVICE
sudo diskutil mount /dev/${USB_DEVICE}s3
cp ~/Downloads/answer.toml /Volumes/PROXMOX-AIS/
```

## 📊 Performance Metrics

### Typical Execution Times
- **File Verification**: < 1 second
- **Partition Creation**: 10-15 seconds  
- **ISO Writing**: 3-5 minutes (depending on USB speed)
- **Answer File Copy**: < 1 second
- **Total Time**: ~5-7 minutes

### Hardware Requirements
- **USB Device**: 8GB+ capacity recommended
- **USB Speed**: USB 3.0+ for optimal performance
- **Available Space**: ~2GB minimum for Proxmox ISO

## 🔄 Script Variants

### Node-Specific Versions
The script can be adapted for different nodes by modifying:
- Answer file names (`answer-node-02.toml`)
- ISO names (if using node-specific ISOs)
- Configuration validation

### Automation Integration
For complete automation, the script supports:
- Non-interactive mode (with appropriate modifications)
- Batch processing for multiple USBs
- Integration with CI/CD pipelines

---

**Status**: ✅ **PRODUCTION-TESTED** - Successfully used for Cooper'n'80s node deployments  
**Compatibility**: macOS 10.15+ with diskutil support  
**Integration**: Complete integration with Vault secrets and cooper.lab infrastructure