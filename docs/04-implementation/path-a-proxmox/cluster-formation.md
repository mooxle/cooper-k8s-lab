# Proxmox Cluster Formation

> Manual cluster creation for Cooper'n'80s three-node infrastructure

## 🎯 Overview

After automated individual node deployment, the Proxmox nodes are manually formed into a cluster for centralized management, shared configuration, and high availability capabilities.

## 📋 Prerequisites

- ✅ All 3 nodes deployed via automated pipeline
- ✅ SSH key authentication operational
- ✅ Network connectivity verified (10.0.1.10-12)
- ✅ Hostname resolution working (cooper.lab domain)

## 🔧 Cluster Formation Process

### Step 1: Create Initial Cluster (Node-01)

Connect to the first node and initialize the cluster:

```bash
# SSH to cooper-node-01
ssh -i ~/.ssh/cooper-node-01_key root@10.0.1.10

# Create cluster (manual step required)
pvecm create cooper-cluster

# Verify cluster status
pvecm status
```

**Expected Output:**
```
Cluster information
-------------------
Name:             cooper-cluster
Config Version:   1
Transport:        knet
Secure auth:      on

Quorum information
------------------
Date:             Sun Aug 25 15:30:00 2025
Quorum provider:  corosync_votequorum
Nodes:            1
Node ID:          0x00000001
Ring ID:          1.4
Quorate:          Yes

Votequorum information
----------------------
Expected votes:   1
Highest expected: 1
Total votes:      1
Quorum:           1  
Flags:            Quorate

Membership information
----------------------
    Nodeid      Votes Name
0x00000001          1 10.0.1.10 (local)
```

### Step 2: Join Additional Nodes

**Join Node-02:**
```bash
# SSH to cooper-node-02
ssh -i ~/.ssh/cooper-node-02_key root@10.0.1.11

# Join cluster
pvecm add 10.0.1.10

# Verify membership
pvecm nodes
```

**Join Node-03:**
```bash
# SSH to cooper-node-03  
ssh -i ~/.ssh/cooper-node-03_key root@10.0.1.12

# Join cluster
pvecm add 10.0.1.10

# Verify membership
pvecm nodes
```

### Step 3: Cluster Validation

**Check cluster status from any node:**
```bash
# Cluster overview
pvecm status

# Node list
pvecm nodes

# Quorum status
pvecm expected

# Corosync status
systemctl status corosync
```

**Expected Final Status:**
```
Quorum information
------------------
Date:             Sun Aug 25 16:00:00 2025
Quorum provider:  corosync_votequorum
Nodes:            3
Node ID:          0x00000001
Ring ID:          1.8
Quorate:          Yes

Votequorum information
----------------------
Expected votes:   3
Highest expected: 3
Total votes:      3
Quorum:           2  
Flags:            Quorate

Membership information
----------------------
    Nodeid      Votes Name
0x00000001          1 10.0.1.10 (local)
0x00000002          1 10.0.1.11
0x00000003          1 10.0.1.12
```

## 🌐 Network Configuration Verification

### Cluster Network Requirements

**Corosync Communication:**
- **Primary Ring**: 10.0.1.0/24 (management network)  
- **Ports**: 5405/UDP (corosync), 22/TCP (SSH), 8006/TCP (web interface)
- **Multicast**: Automatic discovery within VLAN

**Validation Commands:**
```bash
# Check corosync ring status
corosync-quorumtool -s

# Verify network connectivity between nodes
for node in 10.0.1.10 10.0.1.11 10.0.1.12; do
  echo "Testing connectivity to $node:"
  ping -c 2 $node
done

# Check cluster configuration
cat /etc/pve/corosync.conf
```

## 🔍 Troubleshooting Common Issues

### Issue: Cluster Creation Fails
**Symptom:** `pvecm create` returns errors
**Solution:**
```bash
# Check corosync service
systemctl status corosync

# Verify hostname resolution
hostname -f
getent hosts $(hostname -f)

# Check firewall (should be disabled in lab)
systemctl status firewalld
```

### Issue: Node Join Fails
**Symptom:** `pvecm add` cannot connect to cluster
**Solutions:**
```bash
# Verify SSH connectivity to cluster node
ssh root@10.0.1.10

# Check corosync status on cluster node
systemctl status corosync

# Force cluster configuration update
pvecm updatecerts
```

### Issue: Split Brain / Quorum Lost
**Symptom:** Cluster shows "No quorum" 
**Solution:**
```bash
# Check current quorum status
pvecm status

# If 2 nodes active but no quorum, adjust expected votes
pvecm expected 2

# After network recovery, reset to proper value
pvecm expected 3
```

## 📊 Cluster Benefits

### Centralized Management
- **Single Web Interface**: Access all nodes from any cluster member
- **Unified Configuration**: Shared users, certificates, storage pools
- **Live Migration**: VM migration between nodes (with shared storage)

### High Availability 
- **Automatic Failover**: VMs restart on healthy nodes if host fails
- **Distributed Configuration**: Cluster config replicated to all nodes
- **Quorum Protection**: Prevents split-brain scenarios

### Operational Efficiency
- **Bulk Operations**: Manage multiple nodes simultaneously
- **Centralized Monitoring**: Cluster-wide resource monitoring
- **Unified Backup**: Coordinated backup scheduling across nodes

## 🔐 Security Considerations

### Certificate Management
```bash
# Update cluster certificates (run on any node)
pvecm updatecerts

# Verify certificate distribution
ls -la /etc/pve/nodes/*/pve-ssl.pem
```

### Access Control
- **Cluster-wide users**: User accounts replicated across all nodes
- **Shared authentication**: Single sign-on across cluster
- **Permission inheritance**: Role-based access control

## 🚀 Post-Cluster Configuration

### Shared Storage (Future)
```bash
# Add shared storage (NFS/Ceph) for live migration
pvesm add nfs shared-storage --server 10.0.1.100 --export /mnt/storage

# Verify storage visibility
pvesm status
```

### Backup Configuration
```bash
# Configure cluster-wide backup storage
pvesm add dir backup-storage --path /var/lib/vz/dump --content backup

# Schedule coordinated backups
vzdump --all --storage backup-storage --compress gzip
```

## 📋 Maintenance Procedures

### Cluster Health Checks
```bash
# Daily cluster validation
pvecm status | grep -E "(Quorate|Total votes|Expected)"

# Node communication test
corosync-quorumtool -l

# Web interface accessibility
curl -k https://10.0.1.10:8006/api2/json/version
```

### Node Maintenance Mode
```bash
# Put node in maintenance (migrate VMs first)
pvecm nodes

# Remove node temporarily (if needed)
# pvecm delnode <nodename>

# Add node back after maintenance
# pvecm add <ip-address>
```

---

**Status**: ✅ **OPERATIONAL** - Three-node Proxmox cluster active with proper quorum  
**Next Phase**: VM deployment and testing across cluster nodes  
**Benefits**: Centralized management, HA capability, unified configuration