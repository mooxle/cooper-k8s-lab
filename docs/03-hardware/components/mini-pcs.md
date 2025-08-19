# Mini PC Computing Nodes

> Primary compute platform for Cooper'n'80s Kubernetes cluster

## 🎯 Executive Summary

**Decision**: Intel i5-10500T (6C/12T) with 16-32GB RAM and 512GB NVMe  
**Rationale**: Hyperthreading advantage enables 4-6 VMs vs 2-3 VMs without HT  
**Result**: 150-250 pod cluster capacity with comfortable learning environment  

> *"The difference between 6 and 12 threads isn't just mathematical - it's the difference between a functional lab and a comfortable learning environment."*

## 📋 Target Specification

### Mandatory Requirements
- **CPU**: Intel i5-10500T (6 cores, 12 threads)
- **RAM**: 16GB minimum (32GB preferred, upgradeable)
- **Storage**: 512GB NVMe SSD
- **Platform**: Dell OptiPlex preferred, HP/Lenovo/Fujitsu acceptable
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

## 💾 Memory Configuration

### 16GB vs 32GB Analysis

| Aspect | 16GB Configuration | 32GB Configuration |
|--------|-------------------|-------------------|
| **VM Sizing** | 2-3 VMs @ 4-6GB each | 3-4 VMs @ 6-8GB each |
| **Pod Capacity** | ~150-200 total pods | ~250-300 total pods |
| **Monitoring** | Basic metrics only | Full Prometheus/Grafana |
| **CI/CD** | Limited runners | Multiple Jenkins agents |
| **Cost Impact** | Base price | +€100 per node |
| **Upgrade Path** | ✅ SODIMM upgradeable | Final configuration |

**Recommended Start**: 16GB with upgrade when needed

## 💾 Storage Strategy

### Capacity Requirements

**Path A (Proxmox + K3s)**:
```bash
Proxmox Host OS:       ~20GB
VM Images (6 VMs):     ~200-300GB
Container Images:      ~50GB
Snapshots/Backups:    ~100GB
Growth/Logs:          ~50GB
Total per Node:       ~430-530GB
```

**Path B (OKD + KubeVirt)**:
```bash
CoreOS/FCOS:          ~20GB
OKD Platform:         ~50GB
Container Registry:   ~100GB
KubeVirt Images:      ~150GB
Growth:               ~100GB
Total per Node:       ~450GB
```

**Recommended**: 512GB NVMe - optimal balance for both paths

## 🔍 Platform Comparison

### CPU Options Evaluated

| CPU | Cores/Threads | Architecture | Virtualization | Verdict |
|-----|---------------|--------------|----------------|---------|
| **Intel i5-10500T** | 6C/12T | 14nm Comet Lake | VT-x, VT-d | ✅ **Selected** |
| Intel i5-9500T | 6C/6T | 14nm++ Coffee Lake | VT-x, VT-d | ❌ No Hyperthreading |
| AMD Ryzen V1756B | 4C/8T | 7nm Zen+ | AMD-V, IOMMU | ❌ Fewer cores |

### Platform Preferences

**Dell OptiPlex** (Primary choice):
- Proven experience with similar models
- Good Linux compatibility
- Consistent availability in used market
- Standard form factors

**Alternatives** (Acceptable):
- HP EliteDesk series
- Lenovo ThinkCentre M-series
- Fujitsu Esprimo series

## 🎯 Cluster Capacity Planning

### VM Allocation Strategy

**Lab-Optimized (1.5x CPU Overcommit)**:
```
Node 1: Control+Worker (6 vCPU, 6GB) + Worker (6 vCPU, 8GB)
Node 2: Control+Worker (6 vCPU, 6GB) + Worker (6 vCPU, 8GB)  
Node 3: Control+Worker (6 vCPU, 6GB) + Worker (6 vCPU, 8GB)

Cluster Total: 36 vCPU, 42GB RAM, 6 VMs
Pod Capacity: ~150-200 pods across cluster
```

**Aggressive Lab (2x CPU Overcommit)**:
```
Per Node: 3 VMs (8 vCPU each) = 24 vCPU per physical node
Cluster Total: 72 vCPU, 42GB RAM, 9 VMs
Pod Capacity: ~200-250 pods across cluster
```

## 📊 Expected Performance

### K3s Resource Allocation

**Control+Worker Node** (6-8 vCPU, 5-6GB):
- System + K3s overhead: ~1.5GB RAM, 1.3 vCPU
- Available for pods: ~4GB RAM, 5-7 vCPU
- Estimated pods: 20-25 per node

**Pure Worker Node** (6-8 vCPU, 5-8GB):
- System + K3s overhead: ~1.3GB RAM, 0.8 vCPU
- Available for pods: ~4-6GB RAM, 5-7 vCPU
- Estimated pods: 25-35 per node

## 🛒 Procurement Strategy

### Current Status
- **Status**: ⚪ Pending procurement
- **Target**: 3x identical Mini PCs
- **Source**: Commercial supplier relationship established

### Selection Criteria
1. **CPU verification** - Confirm i5-10500T specifically
2. **Memory configuration** - 16GB minimum, check upgradeability
3. **Storage type** - NVMe preferred, SATA acceptable
4. **Physical condition** - Cosmetic issues acceptable, functional critical
5. **Accessories** - Power adapters included

## 🔧 Integration Planning

### Rack Mounting
- **Mount type**: Custom 3D-printed brackets (PETG for heat resistance)
- **Ventilation**: 15mm minimum clearance between units
- **Cable access**: Rear cable management with tie-down points
- **Power**: Individual IEC connections to 1U PDU

### Network Connectivity
- **Interface**: Gigabit Ethernet (built-in)
- **Cables**: 0.25-0.5m patch cables to switch
- **VLAN**: Untagged VLAN 10 (10.0.1.0/24)
- **IP Assignment**: DHCP reservations (10.0.1.10-12)

## 📚 Related Documentation

- **[Rack Design](rack.md)** - Physical mounting specifications
- **[Networking](networking.md)** - Connectivity requirements
- **[Shopping List](../shopping-list.md)** - Budget and purchase tracking

---

**Bottom Line**: i5-10500T + 16GB provides excellent learning foundation with clear upgrade path, while Hyperthreading delivers the threading density essential for realistic container workloads.