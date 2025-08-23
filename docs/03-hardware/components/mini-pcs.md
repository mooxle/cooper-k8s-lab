# Mini PC Computing Nodes

> Primary compute platform for Cooper'n'80s Kubernetes cluster

## 🎯 Executive Summary

**Decision**: Intel i5-10500T (6C/12T) with 32GB RAM and 512GB NVMe  
**Rationale**: Hyperthreading advantage enables 4-6 VMs vs 2-3 VMs without HT  
**Result**: 300-400 pod cluster capacity with comprehensive learning environment  

> *"The difference between 6 and 12 threads isn't just mathematical - it's the difference between a functional lab and a transformative learning environment."*

## 📋 Target Specification

### Mandatory Requirements
- **CPU**: Intel i5-10500T (6 cores, 12 threads)
- **RAM**: 32GB DDR4 (final configuration)
- **Storage**: 512GB NVMe SSD
- **Platform**: Dell OptiPlex 3080 Micro (confirmed)
- **Virtualization**: VT-x, VT-d support (Intel standard)

### Why i5-10500T Specifically

**Hyperthreading Advantage**:
```bash
Without HT (i5-9500T): 6 physical cores → 6 vCPUs → 2-3 VMs possible
With HT (i5-10500T):   6 physical cores → 12 vCPUs → 4-6 VMs possible
```

**Kubernetes Benefits**:
- 2x more pod scheduling slots
- Better container density
- Realistic enterprise workload simulation
- Support for complex multi-service applications

## 💾 Memory Configuration

### 32GB Final Specification (UPDATED)

| Aspect | 32GB Configuration | Benefits |
|--------|-------------------|----------|
| **VM Sizing** | 3-4 VMs @ 6-8GB each | Comfortable resource allocation |
| **Pod Capacity** | ~300-400 total pods | Enterprise-scale simulation |
| **Monitoring** | Full Prometheus/Grafana stack | Complete observability |
| **CI/CD** | Multiple Jenkins agents + runners | Realistic development workflows |
| **Databases** | PostgreSQL, MySQL, Redis clusters | Production-like data services |
| **Service Mesh** | Istio with full feature set | Advanced networking patterns |

**Memory Upgrade Impact**:
```
Original Plan:  3x 16GB = 48GB total cluster memory
Final Order:    3x 32GB = 96GB total cluster memory
Performance:    2x memory capacity = enterprise-grade capabilities
Learning Value: Realistic multi-service application deployments
```

## 💾 Storage Strategy

### Capacity Requirements (UPDATED)

**Path A (Proxmox + K3s)**:
```bash
Proxmox Host OS:       ~20GB
VM Images (6 VMs):     ~300-400GB (larger VMs with 32GB support)
Container Images:      ~100GB (more diverse workloads)
Snapshots/Backups:    ~150GB (production-like backup strategy)
Growth/Logs:          ~50GB
Total per Node:       ~620-720GB (within 1TB capacity with headroom)
```

**Path B (OKD + KubeVirt)**:
```bash
CoreOS/FCOS:          ~20GB
OKD Platform:         ~100GB (full enterprise features)
Container Registry:   ~200GB (comprehensive image library)
KubeVirt Images:      ~200GB (multiple OS templates)
Growth:               ~150GB
Total per Node:       ~670GB (comfortable within 1TB)
```

**Recommendation**: 512GB sufficient for learning objectives, 1TB optimal for production simulation

## 🔍 Platform Comparison

### CPU Options Evaluated

| CPU | Cores/Threads | Architecture | Virtualization | Verdict |
|-----|---------------|--------------|----------------|---------|
| **Intel i5-10500T** | 6C/12T | 14nm Comet Lake | VT-x, VT-d | ✅ **Selected** |
| Intel i5-9500T | 6C/6T | 14nm++ Coffee Lake | VT-x, VT-d | ❌ No Hyperthreading |
| AMD Ryzen V1756B | 4C/8T | 7nm Zen+ | AMD-V, IOMMU | ❌ Fewer cores |
| Intel i7-10700T | 8C/16T | 14nm Comet Lake | VT-x, VT-d | ❌ Overkill for lab scale |

### Platform Selection

**Dell OptiPlex 3080 Micro** (Confirmed):
- **Business Relationship**: Established supplier with excellent support
- **Specification Match**: Exact i5-10500T + 32GB + 512GB configuration
- **Linux Compatibility**: Proven track record with enterprise Linux
- **Form Factor**: Perfect fit for custom rack design
- **Expandability**: Upgradeable components for future needs

## 🎯 Cluster Capacity Planning

### VM Allocation Strategy (UPDATED)

**Enterprise Lab Configuration (32GB per node)**:
```
Node 1 (Control+Worker): 
├── Control VM: 6 vCPU, 8GB RAM (K8s master services)
└── Worker VM:  6 vCPU, 8GB RAM (application workloads)

Node 2 (Pure Worker): 
├── Worker VM:  6 vCPU, 10GB RAM (database services)
└── Worker VM:  6 vCPU, 10GB RAM (monitoring stack)

Node 3 (Pure Worker):
├── Worker VM:  6 vCPU, 10GB RAM (CI/CD services)  
└── Worker VM:  6 vCPU, 10GB RAM (development tools)

Cluster Total: 36 vCPU, 66GB allocated (96GB available)
Pod Capacity: ~300-400 pods across cluster
Reserved:     30GB for host OS and overhead
```

**Resource Efficiency**:
- **Memory Utilization**: ~70% for workloads, 30% for system overhead
- **CPU Overcommit**: 1.5x ratio for realistic enterprise patterns
- **Storage Distribution**: Local NVMe for performance, shared storage via NFS/Ceph

### Enterprise Workload Examples (NEW)

**Development Environment**:
- **GitLab/Forgejo**: Version control with CI/CD runners
- **Harbor Registry**: Container image management
- **SonarQube**: Code quality and security analysis
- **Nexus Repository**: Artifact management

**Data Services**:
- **PostgreSQL Cluster**: Primary database with HA
- **Redis Cluster**: Caching and session storage
- **Elasticsearch Stack**: Logging and search
- **MinIO**: S3-compatible object storage

**Monitoring & Observability**:
- **Prometheus**: Metrics collection and storage
- **Grafana**: Visualization and dashboarding
- **AlertManager**: Alert routing and management
- **Jaeger**: Distributed tracing

**Service Mesh & Security**:
- **Istio**: Advanced traffic management
- **Vault**: Secrets management integration
- **Falco**: Runtime security monitoring
- **OPA Gatekeeper**: Policy enforcement

## 📊 Expected Performance (UPDATED)

### K3s Resource Allocation (32GB nodes)

**Control+Worker Node** (6-8 vCPU, 8GB per VM):
- System + K3s overhead: ~2GB RAM, 1.5 vCPU
- Available for pods: ~6GB RAM, 6.5 vCPU per VM
- Estimated pods: 30-40 per VM (60-80 per node)

**Pure Worker Node** (6-8 vCPU, 10GB per VM):
- System + K3s overhead: ~1.5GB RAM, 1 vCPU
- Available for pods: ~8.5GB RAM, 7 vCPU per VM
- Estimated pods: 40-50 per VM (80-100 per node)

**Cluster Totals**:
- **Total Pod Capacity**: 300-400 pods
- **Concurrent Services**: 50-100 different applications
- **Database Workloads**: Multiple HA database clusters
- **Monitoring Overhead**: 15-20% of resources for observability

## 🛒 Procurement Strategy (COMPLETED)

### Final Status ✅
- **Status**: **DELIVERED** - Dell OptiPlex 3080 Micro (3x units)
- **Specifications**: 3x i5-10500T, **32GB RAM**, 512GB NVMe
- **Supplier**: Business contact relationship
- **Delivery**: Completed as scheduled

### Procurement Success Factors
1. **CPU Verification**: ✅ Confirmed i5-10500T specifically
2. **Memory Upgrade**: ✅ 32GB DDR4 (doubled from original 16GB plan)
3. **Storage Type**: ✅ NVMe SSD for optimal performance
4. **Physical Condition**: ✅ Excellent condition, fully functional
5. **Accessories**: ✅ Power adapters and documentation included

## 🔧 Integration Planning (UPDATED)

### Rack Mounting (UPDATED)
- **Node Positions**: 3U, 4U, 5U (Dell OptiPlex 3080 Micro)
- **Mount Type**: Custom 3D-printed PETG brackets (heat resistance)
- **Ventilation**: 15mm minimum clearance between units
- **Cable Access**: Rear cable management with integrated routing
- **Power**: Individual adapters in dedicated 1U power tray

### Network Connectivity (UPDATED)
- **Interface**: Gigabit Ethernet (built-in)
- **Cable Runs**: 
  - Node 1 (3U) → Switch Port 1: 0.25m orange patch cable
  - Node 2 (4U) → Switch Port 2: 0.5m orange patch cable  
  - Node 3 (5U) → Switch Port 3: 0.5m orange patch cable
- **VLAN**: Untagged VLAN 10 (10.0.1.0/24)
- **IP Assignment**: DHCP reservations via Cooper DNS/DHCP stack
- **DNS Integration**: Automatic registration in cooper.lab domain

### Power Management (UPDATED)
**1U Power Tray Configuration**:
```
┌─────────────────────────────────────────────────┐
│  [PSU 1]     [PSU 2]     [PSU 3]     [Cable]   │ 1U Tray
│   Node 1     Node 2     Node 3      Mgmt       │
└─────────────────────────────────────────────────┘
       │          │          │          │
       ▼          ▼          ▼          ▼
   [IEC Cable][IEC Cable][IEC Cable][Power Strip]
       │          │          │          │
       └──────────┴──────────┴──────────┘
                      │
              Rear-mounted PDU
```

**Power Distribution**:
- **Individual Power**: Each Mini PC has dedicated 65W adapter
- **Tray Organization**: 3D-printed organizer for clean power management
- **Cable Routing**: IEC cables from tray to rear PDU
- **Redundancy**: No shared power dependencies between nodes
- **Total Power**: ~200W maximum draw for all three nodes

### Service Discovery Integration (NEW)
**Cooper.lab Domain Integration**:
- **Node 1**: `node-01.cooper.lab` (10.0.1.10) - Control+Worker
- **Node 2**: `node-02.cooper.lab` (10.0.1.11) - Worker
- **Node 3**: `node-03.cooper.lab` (10.0.1.12) - Worker
- **Automatic DNS**: DHCP lease triggers DNS record creation
- **Service Access**: SSH via `ssh node-01.cooper.lab`
- **Application URLs**: `app.cooper.lab` for ingress services

### Cluster Configuration (UPDATED)

**Node Roles**:
```
Node 1 (3U): Control Plane + Worker
├── Kubernetes Master Services (etcd, api-server, scheduler)
├── Worker capabilities for system services
├── Ingress controller and load balancer
└── Monitoring and logging infrastructure

Node 2 (4U): Pure Worker  
├── Application workloads
├── Database services (PostgreSQL, Redis)
├── Development tools and CI/CD
└── Storage services (MinIO, NFS)

Node 3 (5U): Pure Worker
├── Compute-intensive workloads  
├── Data processing and analytics
├── Additional application replicas
└── Backup and disaster recovery services
```

**High Availability Strategy**:
- **Control Plane**: Single master for lab simplicity, external etcd backup
- **Application Services**: Multi-replica deployments across worker nodes
- **Data Services**: Persistent volumes with node affinity
- **Network Services**: Ingress controller HA via service LoadBalancer

## 📈 Performance Expectations (UPDATED)

### Realistic Workload Scenarios

**Development Environment** (~100 pods):
- GitLab instance with runners
- Harbor container registry
- Development databases
- Monitoring stack (Prometheus/Grafana)

**Production Simulation** (~200-300 pods):
- Multi-tier applications (frontend, API, database)
- Service mesh (Istio) with full features
- Comprehensive monitoring and logging
- CI/CD pipelines with parallel builds

**Learning Laboratory** (~50-150 pods):
- Kubernetes training scenarios
- Application deployment experiments
- Infrastructure automation testing
- Disaster recovery simulations

### Resource Monitoring

**Key Performance Indicators**:
- **Pod Density**: Target 100+ pods per node
- **Memory Utilization**: 70-80% sustainable utilization
- **CPU Load**: 60-70% average with burst capacity
- **Storage I/O**: NVMe provides excellent throughput for container workloads

**Scaling Indicators**:
- **Memory Pressure**: First constraint in pod scheduling
- **Network Throughput**: Gigabit adequate for lab scale
- **Storage Growth**: Monitor for log and image accumulation

## 📚 Related Documentation

- **[Rack Design](rack.md)** - Physical mounting specifications and power tray
- **[Networking](networking.md)** - Connectivity requirements and cooper.lab integration
- **[Shopping List](../shopping-list.md)** - Budget and purchase tracking
- **[Network Services](../../02-design/network-services.md)** - DNS/DHCP integration

---

**Bottom Line**: i5-10500T + 32GB provides **enterprise-grade learning foundation** with realistic resource allocation, while Hyperthreading and memory capacity deliver the performance density essential for **comprehensive Kubernetes education** and **production-pattern simulation**.