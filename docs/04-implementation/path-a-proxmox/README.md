# Path A: Kubernetes ON Virtualization

> Complete automation from bare-metal to production-ready Proxmox nodes

## 🎯 Implementation Strategy

**Architecture**: `Hardware → Proxmox → VMs → K3s Cluster → Container Workloads`

**Automation Level**: 85% end-to-end (44-minute deployment cycle)  
**Security**: Enterprise-grade with Vault integration  
**Methodology**: Infrastructure as Code with systematic validation



## 📋 Implementation Components

| Document | Purpose | Status |
|----------|---------|--------|
| **[Deployment Guide](automated-deployment.md)** | Complete automation pipeline | ✅ Operational |
| **[Auto-Install Process](autoinstall-process.md)** | Official Proxmox tooling workflow | ✅ Tested |
| **[Cluster Formation](cluster-formation.md)** | Manual cluster creation procedures | ✅ Operational |
| **[Terraform Integration](terraform/)** | Vault secrets and infrastructure | 🟡 In Progress |
| **[Ansible Playbooks](ansible/)** | Post-deployment automation | 🟡 In Progress |

### Automation Components

| Component | Purpose | Status |
|-----------|---------|--------|
| **[Ansible Inventory](automation/ansible/inventory.md)** | Node configuration and connectivity | ✅ Production |
| **[Ansible Playbooks](automation/ansible/playbooks.md)** | Complete automation suite | ✅ Production |
| **[USB Creation Script](automation/scripts/usb-creation.md)** | macOS USB media creation | ✅ Production |

## 🚀 Deployment Pipeline

### Complete Automation Flow

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

### Key Achievement Metrics

- **⚡ Deployment Time**: 44 minutes bare-metal → SSH-accessible
- **🔐 Security Level**: SSH-only access, automated hardening
- **📋 Automation**: 85% hands-off deployment
- **🎯 Reproducibility**: Template-driven, identical deployments

## 🔧 Technical Implementation

### Infrastructure as Code Stack
- **Terraform**: Vault secret generation and infrastructure provisioning
- **Vault**: Dynamic credential management with Ed25519 SSH keys
- **Proxmox Tools**: Official `proxmox-auto-install-assistant`
- **Ansible**: Post-deployment configuration and hardening

### Hardware Integration
- **Target Platform**: Dell OptiPlex 3080 Micro (i5-10500T, 32GB RAM)
- **Network Integration**: Cooper.lab DNS with automatic registration
- **Storage**: Local NVMe with enterprise backup strategies

### Enterprise Foundation Implementation (NEW)

#### ZFS Encrypted Storage Foundation
**Status**: ✅ **OPERATIONAL** - 1TB+ encrypted storage cluster-wide

```yaml
storage_implementation:
  architecture: "ZFS-over-LVM with Vault-managed encryption"
  capacity_per_node: "350GB encrypted storage"
  total_cluster_capacity: "~1TB with enterprise encryption"
  
  encryption_details:
    algorithm: "AES-256-GCM"
    key_management: "HashiCorp Vault integration"
    key_isolation: "Unique per-node encryption keys"
    key_format: "32-byte raw binary (SHA256-derived)"
  
  performance_features:
    compression: "LZ4 (optimal CPU/storage trade-off)"
    caching: "ZFS ARC up to 16GB per node"
    snapshots: "Copy-on-write with encryption preservation"
    deduplication: "Available but not enabled (RAM considerations)"
  
  vault_integration:
    secret_path: "cooper-n-80s/environments/dev/zfs-keys/{node-name}"
    key_generation: "Automated with timestamp+hostname+IP entropy"
    lifecycle_management: "Centralized rotation and audit trail"
```

**K3s Benefits**:
- VM disk images stored on encrypted ZFS pools
- Snapshot-based backup strategies for K3s VMs
- Compression reduces storage overhead for container images
- Enterprise-grade security with centralized key management

#### VXLAN + EVPN Overlay Networking
**Status**: ✅ **OPERATIONAL** - BGP EVPN control plane with validated L2 connectivity

```yaml
network_implementation:
  control_plane: "BGP EVPN AS 65001 (full-mesh iBGP)"
  data_plane: "VXLAN VNI 100 over underlay 10.0.1.0/24"
  
  vtep_endpoints:
    cooper-node-01: "10.0.1.10 (router-id)"
    cooper-node-02: "10.0.1.11 (router-id)" 
    cooper-node-03: "10.0.1.12 (router-id)"
  
  route_exchange:
    type_2_routes: "MAC/IP advertisements for unicast forwarding"
    type_3_routes: "IMET routes for BUM traffic handling"
    route_targets: "RT:65001:100 for VNI 100"
  
  integration:
    proxmox_bridge: "vmbr1 with vxlan100 attachment"
    vm_connectivity: "Ready for K3s VM deployment"
    bridge_learning: "Disabled (EVPN-controlled)"
    arp_suppression: "Enabled for unicast optimization"
```

**K3s Integration Benefits**:
- VMs connect via vmbr1 bridge with overlay networking
- Multi-tenant capabilities (different VNIs for different workloads)
- Scalable L2 connectivity without broadcast flooding
- Enterprise SDN patterns ready for container networking

#### Operational Readiness for K3s Deployment

**Storage Readiness**:
- ✅ Proxmox storage pool `cooper-encrypted` registered
- ✅ VM templates can be deployed on encrypted storage
- ✅ Snapshot/backup procedures established
- ✅ Performance baseline documented

**Network Readiness**:
- ✅ Cross-node L2 connectivity validated (10.0.200.0/24 test network)
- ✅ BGP EVPN sessions established and stable
- ✅ VM attachment procedures to vmbr1 bridge
- ✅ Integration with existing DNS/DHCP services

**Next Steps for K3s**:
1. **VM Template Creation**: Ubuntu 22.04 LTS on encrypted storage
2. **K3s Node Deployment**: Control plane and workers on overlay network
3. **CNI Integration**: Kubernetes overlay on VXLAN foundation
4. **Storage Classes**: ZFS-backed persistent volumes for workloads

## 🔐 Security Implementation

### Enterprise Security Patterns
```yaml
security_layers:
  credential_management:
    vault_integration: "Dynamic secrets with rotation"
    ssh_keys: "Ed25519 per-node isolation"
    password_policy: "24-character secure generation"
  
  network_security:
    access_method: "SSH keys only (passwords disabled)"
    intrusion_prevention: "fail2ban with automated IP blocking"
    network_isolation: "VLAN 10 segmentation"
  
  system_hardening:
    package_management: "Automated updates and patching"
    service_monitoring: "Prometheus node-exporter"
    backup_automation: "Daily retention with validation"
```

## 🚀 Next Phase: K3s Deployment

### Kubernetes Cluster Planning
- **Control Plane**: Node-01 (mixed control+worker)
- **Worker Nodes**: Node-02, Node-03 (pure workers)
- **Pod Capacity**: 300-400 pods across cluster
- **Network CNI**: Calico or Flannel overlay networking

### Enterprise Workload Targets
- **Development Stack**: GitLab, Harbor, SonarQube
- **Data Services**: PostgreSQL, Redis, MinIO clusters
- **Observability**: Prometheus, Grafana, AlertManager
- **Security**: Vault integration, service mesh patterns

## 🎯 Learning Objectives

### Infrastructure as Code Mastery
- **Template-Driven Deployment**: Reproducible infrastructure patterns
- **Security Automation**: Enterprise-grade hardening procedures
- **Operational Excellence**: Monitoring, backup, disaster recovery

### Enterprise Pattern Experience
- **Secrets Management**: HashiCorp Vault production patterns
- **Network Services**: DNS/DHCP integration with service discovery
- **Change Management**: Version-controlled infrastructure evolution

---

**Status**: ✅ **OPERATIONAL** - Ready for Kubernetes cluster deployment  
**Next Phase**: K3s automation and enterprise workload deployment