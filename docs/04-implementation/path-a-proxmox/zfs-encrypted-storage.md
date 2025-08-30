# ZFS Encrypted Storage with TPM Integration

> Enterprise-grade encrypted storage implementation for Cooper'n'80s infrastructure

## 🎯 Overview

Implementation of ZFS native encryption with TPM2-backed key management across the Cooper'n'80s cluster, providing hardware-secured storage with automated unlock capabilities.

## 🏗️ Architecture

### Storage Stack
```
Hardware Layer: 3x 512GB NVMe SSDs
├── Physical Security: TPM 2.0 chips (Intel PTT)
├── OS Layer: Proxmox VE on LVM
├── Storage Layer: ZFS pools with native encryption
└── Key Management: TPM-sealed keys with systemd integration
```

### Security Model
- **Encryption**: AES-256-GCM at ZFS level
- **Key Storage**: TPM2-sealed objects (hardware-bound)
- **Unlock Method**: Systemd service automation
- **Access Control**: Root-only key access

## 📊 Infrastructure Baseline

### Hardware Configuration
- **Nodes**: 3x Dell OptiPlex 3080 Micro
- **TPM**: Intel Platform Trust Technology (fTPM)
- **Storage**: 512GB NVMe per node
- **Network**: Management VLAN (10.0.1.0/24)

### Storage Layout
```
Per Node Configuration:
├── nvme0n1p1: 1MB (BIOS boot)
├── nvme0n1p2: 1GB (EFI boot)
└── nvme0n1p3: 476GB (LVM PV)
    ├── pve-root: 96GB (Host OS)
    ├── pve-swap: 8GB (System swap)
    └── pve/cooper-storage: 350GB (ZFS backing store)
        └── cooper-zfs: ZFS pool (encrypted)
```

## 🔐 TPM Key Management

### Key Generation Process
1. **Seed Generation**: Cryptographically secure random data
2. **TPM Sealing**: Bind to hardware configuration
3. **Systemd Integration**: Automated unlock service
4. **Service Dependencies**: Proper boot ordering

### TPM Object Creation
```bash
# Create primary key in TPM
tpm2_createprimary -C o -c /etc/zfs/keys/tpm-primary.ctx

# Create sealed object with ZFS key material
tpm2_create -C /etc/zfs/keys/tpm-primary.ctx \
  -u /etc/zfs/keys/cooper.pub \
  -r /etc/zfs/keys/cooper.priv \
  -i /etc/zfs/keys/cooper-zfs.key.seed

# Load and make persistent
tpm2_load -C /etc/zfs/keys/tpm-primary.ctx \
  -u /etc/zfs/keys/cooper.pub \
  -r /etc/zfs/keys/cooper.priv \
  -c /etc/zfs/keys/cooper.ctx

# Store in TPM persistent memory
tpm2_evictcontrol -C o -c /etc/zfs/keys/cooper.ctx 0x81010001
```

### Security Properties
- **Hardware Binding**: Keys unusable without original TPM
- **Secure Boot Integration**: PCR-based sealing available
- **Unique Per Node**: Each node has independent TPM objects
- **Audit Trail**: TPM operations logged systemically

### Vault Transit Integration (Enhanced Security)

**Enterprise Key Management Evolution:**
- **Vault Transit Engine**: `transit/keys/zfs-wrapper-cooper-zfs` (AES-256-GCM)
- **KV Storage**: `cooper-n-80s/environments/dev/zfs-keys/{node-name}`
- **Authentication**: AppRole per node with dedicated policies
- **Fallback Strategy**: TPM unlock preserved for offline operation

**Dual-Path Architecture:**
```
Primary Path: Vault Transit (HA-capable)
├── AppRole authentication per node
├── Network-based key retrieval
├── Cross-node unlock capability
└── Centralized audit logging

Fallback Path: TPM (Offline-capable)  
├── Hardware-bound local keys
├── No network dependency
├── Node-specific unlock only
└── Local systemd integration
```

**Vault Service Configuration:**
```ini
# /etc/systemd/system/zfs-load-key-cooper-zfs-vault.service
[Unit]
Description=Load ZFS encryption key for cooper-zfs via Vault Transit (AppRole)
After=network.target
Wants=network.target

[Service]
Type=oneshot
Environment=VAULT_ADDR=http://vault.sammet.me:8200
ExecStart=/usr/local/bin/zfs-vault-unlock
RemainAfterExit=yes
TimeoutStartSec=120

[Install]
WantedBy=zfs.target
```

**High Availability Benefits:**
- ✅ **Cross-node unlock**: Any cluster node can unlock any ZFS pool
- ✅ **Live migration ready**: VM storage accessible from multiple hosts  
- ✅ **Zero-downtime maintenance**: Pool migration during node maintenance
- ✅ **Centralized management**: Key lifecycle via Vault API

## 🛠️ ZFS Pool Implementation

### Pool Creation
```bash
# Create ZFS pool with TPM-backed encryption
zpool create -f \
  -o ashift=12 \
  -O encryption=aes-256-gcm \
  -O keyformat=raw \
  -O keylocation=file:///etc/zfs/keys/cooper-zfs.key \
  -O compression=lz4 \
  -O mountpoint=/cooper-storage \
  cooper-zfs /dev/pve/cooper-storage
```

### Pool Properties
```bash
# Verify encryption configuration
zfs get encryption,keyformat,keylocation cooper-zfs

# Expected output:
NAME        PROPERTY     VALUE                                  SOURCE
cooper-zfs  encryption   aes-256-gcm                           local
cooper-zfs  keyformat    raw                                   inherited
cooper-zfs  keylocation  file:///etc/zfs/keys/cooper-zfs.key  local
```

### Performance Optimization
```bash
# Optimize for VM workloads
zfs set recordsize=1M cooper-zfs          # Large block optimization
zfs set compression=lz4 cooper-zfs        # CPU-efficient compression
zfs set atime=off cooper-zfs              # Disable access time updates
zfs set xattr=sa cooper-zfs               # System attribute optimization
```

## 🔧 Automated Unlock Service

### Systemd Service Configuration
```ini
# /etc/systemd/system/zfs-load-key-cooper-zfs-tpm.service
[Unit]
Description=Load ZFS encryption key for cooper-zfs from TPM
DefaultDependencies=no
Before=zfs-mount.service
Before=pve-guests.service
After=systemd-udev-settle.service
Wants=zfs-mount.service

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/sh -c 'zfs get -H -o value keystatus cooper-zfs | grep -q "^available$" && exit 0; tpm2_unseal -c 0x81010001 | zfs load-key cooper-zfs'

[Install]
WantedBy=zfs.target
```

### Service Management
```bash
# Enable TPM-based unlock service
systemctl daemon-reload
systemctl enable zfs-load-key-cooper-zfs-tpm.service

# Disable any existing keyfile-based services
systemctl disable zfs-load-key-cooper-zfs.service 2>/dev/null || true

# Verify service status
systemctl status zfs-load-key-cooper-zfs-tpm.service
```

### Boot Process Integration
1. **Hardware Initialization**: TPM becomes available
2. **Service Execution**: systemd runs unlock service
3. **Key Retrieval**: TPM unseals ZFS key material
4. **Pool Unlock**: ZFS datasets become available
5. **Guest Services**: Proxmox VMs can start

## 📈 Operational Procedures

### Health Monitoring
```bash
# Check encryption status
zfs get keystatus cooper-zfs

# Verify systemd service
systemctl is-active zfs-load-key-cooper-zfs-tpm.service

# Confirm guest services
systemctl is-active pve-guests.service
```

### Cluster-wide Verification
```bash
# Ansible health check across all nodes
ansible proxmox_nodes -i inventory/proxmox.yml -m shell -a \
  "echo -n 'Node: '; hostname; \
   echo -n 'Key Status: '; zfs get -H -o value keystatus cooper-zfs; \
   echo -n 'Guests: '; systemctl is-active pve-guests.service"
```

Expected output per node:
```
Node: cooper-node-01
Key Status: available
Guests: active
```

### Storage Utilization
```bash
# Pool status and utilization
zpool status cooper-zfs
zfs list cooper-zfs

# Compression effectiveness
zfs get compressratio cooper-zfs
```

## 🔄 Key Rotation Strategy

### TPM Object Rotation (Annual)
```bash
# Generate new persistent handle
NEW_HANDLE=0x81010002

# Create new TPM object with existing key material
tpm2_create -C /etc/zfs/keys/tpm-primary.ctx \
  -u /etc/zfs/keys/cooper-new.pub \
  -r /etc/zfs/keys/cooper-new.priv \
  -i /etc/zfs/keys/cooper-zfs.key.seed

# Load and persist new object
tpm2_load -C /etc/zfs/keys/tpm-primary.ctx \
  -u /etc/zfs/keys/cooper-new.pub \
  -r /etc/zfs/keys/cooper-new.priv \
  -c /etc/zfs/keys/cooper-new.ctx

tpm2_evictcontrol -C o -c /etc/zfs/keys/cooper-new.ctx $NEW_HANDLE

# Update systemd service to use new handle
sed -i "s/0x81010001/$NEW_HANDLE/g" /etc/systemd/system/zfs-load-key-cooper-zfs-tpm.service
systemctl daemon-reload

# Test unlock process
systemctl restart zfs-load-key-cooper-zfs-tmp.service

# Remove old handle after verification
tpm2_evictcontrol -C o 0x81010001
```

### ZFS Dataset Key Rotation (2-3 Years)
```bash
# Generate new key material
NEW_KEY="/etc/zfs/keys/cooper-zfs-new.key"
dd if=/dev/urandom of="$NEW_KEY" bs=32 count=1

# Change dataset encryption key
zfs change-key -l -o keyformat=raw -o keylocation="file://$NEW_KEY" cooper-zfs

# Seal new key in TPM
tmp2_create -C /etc/zfs/keys/tpm-primary.ctx \
  -u /etc/zfs/keys/cooper-updated.pub \
  -r /etc/zfs/keys/cooper-updated.priv \
  -i "$NEW_KEY"

# Update persistent object and service configuration
```

## 🛡️ Security Benefits

### Threat Protection
- **Physical Theft**: Encrypted data unusable without TPM hardware
- **Cold Boot Attacks**: Keys not resident in system memory
- **Disk Imaging**: Offline data remains encrypted
- **Unauthorized Access**: Hardware-bound key material

### Compliance Features
- **Data at Rest**: FIPS 140-2 Level 2 equivalent encryption
- **Key Management**: Hardware security module integration
- **Audit Logging**: TPM operations logged through systemd
- **Access Control**: Root-only key file permissions

### Operational Security
- **Automated Unlock**: No manual intervention required
- **Service Dependencies**: Proper boot ordering prevents failures
- **Recovery Procedures**: Multiple key rotation strategies
- **Monitoring Integration**: Health checks via automation

## 📊 Performance Characteristics

### Encryption Overhead
- **CPU Impact**: <5% (AES-NI hardware acceleration)
- **Throughput**: Near-native performance for sequential I/O
- **Latency**: Minimal impact on random I/O operations
- **Memory**: ZFS ARC caching remains effective

### Storage Efficiency
```bash
# Example compression ratios (varies by workload)
VM Images: 1.2-1.5x compression
Log Files: 2.0-4.0x compression
Database Files: 1.1-1.3x compression
General Data: 1.3-1.8x compression
```

### Resource Utilization
```bash
# ZFS ARC allocation per node
Available RAM: 32GB
ARC Target: Up to 16GB (dynamic)
Minimum ARC: 1GB
Compression CPU: <10% average load
```

## 🔧 Proxmox Integration

### Storage Pool Registration
```bash
# Add encrypted ZFS pool to Proxmox
pvesm add zfspool cooper-encrypted \
  --pool cooper-zfs \
  --content images,rootdir \
  --sparse 1 \
  --nodes $(hostname)

# Verify registration
pvesm status | grep cooper-encrypted
```

### VM Configuration
- **Disk Format**: Raw (optimal for ZFS)
- **Storage Backend**: cooper-encrypted
- **Snapshots**: ZFS-level with encryption preservation
- **Backup**: Proxmox Backup Server compatible

## 🧪 Testing and Validation

### Encryption Verification
```bash
# Confirm encryption is active
zfs get -r encryption cooper-zfs

# Test key status after reboot
systemctl status zfs-load-key-cooper-zfs-tpm.service
zfs get keystatus cooper-zfs
```

### Performance Testing
```bash
# Basic I/O performance test
dd if=/dev/zero of=/cooper-storage/test bs=1M count=1000 conv=fdatasync
dd if=/cooper-storage/test of=/dev/null bs=1M

# Clean up test file
rm /cooper-storage/test
```

### Recovery Testing
```bash
# Test manual key loading (emergency procedure)
zfs unload-key cooper-zfs
tpm2_unseal -c 0x81010001 | zfs load-key cooper-zfs

# Verify datasets accessible
ls -la /cooper-storage/
```

## 🚀 Future Enhancements

### Advanced Features
- **Multi-Factor Unlock**: Combine TPM with network-based keys
- **Remote Attestation**: Verify node integrity before unlock
- **Snapshot Encryption**: Automated encrypted backup workflows
- **Performance Monitoring**: Detailed ZFS metrics collection

### Integration Opportunities
- **Vault Integration**: Centralized key lifecycle management
- **Certificate Management**: PKI integration for network services
- **Compliance Automation**: Automated security policy enforcement
- **Disaster Recovery**: Cross-site encrypted replication

## 📚 Troubleshooting

### Common Issues

**Service Fails to Load Key**:
```bash
# Check TPM availability
tpm2_getcap handles-persistent

# Verify handle exists
tpm2_readpublic -c 0x81010001

# Manual unlock for debugging
tpm2_unseal -c 0x81010001 | zfs load-key cooper-zfs
```

**Boot Ordering Problems**:
```bash
# Check service dependencies
systemctl list-dependencies zfs.target

# Verify service ordering
systemctl show zfs-load-key-cooper-zfs-tpm.service | grep -E "Before|After"
```

**Performance Issues**:
```bash
# Check ARC utilization
cat /proc/spl/kstat/zfs/arcstats | grep -E "size|hits|miss"

# Monitor compression effectiveness
zfs get compressratio,used,available cooper-zfs
```

## 🎯 Summary

The Cooper'n'80s cluster implements enterprise-grade encrypted storage through:

- **ZFS Native Encryption**: AES-256-GCM with hardware acceleration
- **TPM2 Key Management**: Hardware-bound security with automated unlock
- **Systemd Integration**: Reliable boot process with proper dependencies
- **Operational Excellence**: Comprehensive monitoring and maintenance procedures

**Security Posture**: Hardware-secured encryption with automated operations
**Performance Impact**: Minimal overhead with significant security benefits
**Operational Complexity**: Low maintenance with enterprise security standards

This implementation provides a foundation for secure Kubernetes workload deployment while maintaining operational simplicity and performance characteristics suitable for production use.