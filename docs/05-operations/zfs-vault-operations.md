# ZFS Vault Operations Guide

> Enterprise-grade ZFS encryption with HashiCorp Vault integration

## 🎯 Overview

The Cooper'n'80s cluster uses Vault Transit engine for ZFS encryption key management, enabling cross-node pool access for High Availability scenarios while maintaining TPM-based fallback capabilities.

## 🔧 Daily Operations

### Health Validation
```bash
# Cluster-wide ZFS status
ansible all -i inventory/proxmox.yml -m shell -a "zfs get keystatus cooper-zfs"

# Vault service validation
ansible all -i inventory/proxmox.yml -m shell -a "systemctl show zfs-load-key-cooper-zfs-vault.service -p ActiveState -p Result"

# Network stability check
ansible all -i inventory/proxmox.yml -m shell -a "systemctl show ifreload-postboot.service -p Result"

# Bridge configuration verification
ansible all -i inventory/proxmox.yml -m shell -a "ip -o -4 addr show | grep -E 'vmbr0|vmbr1'"
```

### Manual Operations
```bash
# Manual ZFS unlock via Vault (if service fails)
/usr/local/bin/vault-approle-login
VAULT_TOKEN=$(cat /etc/vault/token-$(hostname)) /usr/local/bin/zfs-vault-unlock

# TPM fallback unlock (if Vault unavailable)
systemctl start zfs-load-key-cooper-zfs.service

# Force network reload (if bridges missing)
systemctl start ifreload-postboot.service
```

## 🔒 Security Procedures

### AppRole Token Management
```bash
# Check current token validity
VAULT_TOKEN=$(cat /etc/vault/token-$(hostname)) vault token lookup

# Force token refresh
/usr/local/bin/vault-approle-login

# Generate new secret_id for node (from Vault server)
vault write -f auth/approle/role/$(hostname)-zfs/secret-id
```

### Cross-Node Unlock Testing
```bash
# Test unlock capability from different node
# (Run on node-02 to unlock node-01's pool)
export VAULT_ADDR="http://vault.sammet.me:8200"
vault login -method=userpass username=maxsammet

# Retrieve ciphertext for different node
vault kv get cooper-n-80s/environments/dev/zfs-keys/cooper-node-01

# Decrypt and verify (do not actually load)
vault write -field=plaintext transit/decrypt/zfs-wrapper-cooper-zfs \
  ciphertext="vault:v1:URJrV+KJ3Smv..."
```

### Emergency Access Procedures
```bash
# Vault completely unavailable - use TPM fallback
systemctl start zfs-load-key-cooper-zfs.service

# Network failure - manual bridge configuration
ip link set vmbr0 up
ip addr add 10.0.1.10/24 dev vmbr0  # adjust IP per node
ip route add default via 10.0.1.1

# Manual ZFS unlock with known key (emergency only)
echo "binary_key_material" | zfs load-key cooper-zfs
```

## 📊 Monitoring & Alerts

### Key Metrics
```bash
# Critical health indicators
for node in cooper-node-01 cooper-node-02 cooper-node-03; do
  echo "=== $node ==="
  ssh $node "
    echo 'ZFS Status:' \$(zfs get -H -o value keystatus cooper-zfs)
    echo 'Vault Service:' \$(systemctl show zfs-load-key-cooper-zfs-vault.service -p ActiveState --value)
    echo 'Network Timer:' \$(systemctl show ifreload-postboot.service -p Result --value)
    echo 'Bridge IPs:' \$(ip -o -4 addr show | grep -E 'vmbr0|vmbr1' | awk '{print \$4}')
  "
done
```

### Alert Conditions
- **ZFS keystatus != available**: Pool encryption failed to unlock
- **Vault service != active/success**: Network or authentication issues
- **Missing bridge IPs**: Network configuration problems
- **Timer Result != success**: Boot network reload failed

### Performance Monitoring
```bash
# Vault response times
time vault kv get cooper-n-80s/environments/dev/zfs-keys/cooper-node-01

# ZFS unlock timing
time systemctl restart zfs-load-key-cooper-zfs-vault.service

# Network reload performance
time systemctl start ifreload-postboot.service
```

## 🔄 Maintenance Procedures

### Weekly Vault Health
```bash
# Vault server connectivity from all nodes
ansible all -i inventory/proxmox.yml -m shell -a "ping -c 3 vault.sammet.me"

# AppRole authentication testing
ansible all -i inventory/proxmox.yml -m shell -a "/usr/local/bin/vault-approle-login && echo 'Auth OK'"

# Transit key availability
vault read transit/keys/zfs-wrapper-cooper-zfs
```

### Monthly Security Reviews
```bash
# Audit Vault access patterns
vault read sys/internal/counters/requests

# Review AppRole secret_id rotation needs
for node in cooper-node-01 cooper-node-02 cooper-node-03; do
  vault read auth/approle/role/$node-zfs/secret-id
done

# Validate TPM fallback capability
ansible all -i inventory/proxmox.yml -m shell -a "tpm2_getcap handles-persistent | grep 0x81010001"
```

## 🚨 Troubleshooting Guide

### Vault Service Failures
```bash
# Service won't start - check network first
ping vault.sammet.me
systemctl status networking.service

# Authentication fails - verify credentials
cat /etc/vault/role-id /etc/vault/secret-id
vault write auth/approle/login role_id="$(cat /etc/vault/role-id)" secret_id="$(cat /etc/vault/secret-id)"

# Decrypt fails - check Transit key
vault read transit/keys/zfs-wrapper-cooper-zfs
```

### TPM Fallback Issues
```bash
# TPM not accessible
ls -la /dev/tpm*
systemctl status tcsd.service

# Handle not found
tpm2_getcap handles-persistent
# If missing: recreate from backup key material
```

### Network Configuration Problems
```bash
# Bridges not configured
systemctl status networking.service
systemctl restart ifreload-postboot.service

# Manual bridge recovery
/usr/sbin/ifreload -a
ip addr show vmbr0 vmbr1
```

## 📋 Ansible Automation

### Complete Site Deployment
```bash
# Full infrastructure deployment
ansible-playbook -i inventory/proxmox.yml site.yml

# Targeted ZFS + Vault deployment
ansible-playbook -i inventory/proxmox.yml site.yml -t vault,zfs

# Network stability deployment
ansible-playbook -i inventory/proxmox.yml site.yml -t network

# Health-validated reboot cycle
ansible-playbook -i inventory/proxmox.yml site.yml -t reboot,health
```

### Operational Playbooks
```bash
# ZFS health check across cluster
ansible-playbook -i inventory/proxmox.yml playbooks/health-zfs.yml

# Network timer deployment
ansible-playbook -i inventory/proxmox.yml playbooks/network-timer.yml

# System cleanup and hardening
ansible-playbook -i inventory/proxmox.yml playbooks/cleanup.yml
```

## 🎯 Best Practices

### Service Dependencies
- **Never bind to network-online.target**: Causes boot ordering cycles
- **Use network.target only**: Sufficient for Vault connectivity
- **Timer-based post-boot**: Eliminates dependency complexity
- **Fallback services**: Always maintain offline operation capability

### Security Patterns
- **AppRole per node**: Unique authentication scope
- **Short-lived tokens**: Automatic token refresh via helper scripts
- **Fallback strategies**: Multiple unlock paths for resilience
- **Audit logging**: All operations tracked in Vault

### Operational Excellence
- **Systematic validation**: Health checks after every change
- **Staggered operations**: One node at a time for cluster stability
- **Documentation**: All procedures reproducible via automation
- **Monitoring**: Continuous health validation

---

**Operational Philosophy**: *"Enterprise reliability emerges from systematic procedures, comprehensive validation, and elegant fallback strategies - applied with scientific precision to infrastructure operations."*

**Status**: Production-ready ZFS encryption with Vault integration and robust network stability