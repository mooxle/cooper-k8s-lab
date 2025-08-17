# Mini PC Selection - Hardware Analysis

> Rational hardware selection for Cooper'n'80s Kubernetes cluster nodes.

## 📋 Executive Cooper Summary

> 🤓 *"I've calculated all possible configurations. The conclusion is both logical and inevitable."*

**The Decision:**
- **CPU**: Intel i5-10500T (6 cores, 12 threads) - **mandatory for Hyperthreading**
- **RAM**: 16GB or 32GB (market-dependent, both viable)
- **Platform**: Dell OptiPlex preferred, but HP/Lenovo/Fujitsu acceptable
- **Result**: 150-250 pod cluster capacity with room for real learning scenarios

**Why This Matters:**
- Hyperthreading **doubles** VM density (12 vs 6 threads = 4-6 VMs vs 2-3 VMs)
- 16GB sufficient for serious K3s learning, 32GB enables advanced scenarios
- Platform flexibility allows for better pricing opportunities

**Bottom Line:** This configuration transforms the lab from "functional" to "comfortable learning environment" without breaking the budget.

> 🎭 *"Bazinga! Sometimes the best solution is hiding in plain sight - specifically in the CPU specifications."*

---

## 🔍 CPU Architecture Comparison

### Platform Options
| CPU | Cores/Threads | Base/Boost Clock | Architecture | Virtualization |
|-----|---------------|------------------|--------------|----------------|
| **AMD Ryzen V1756B** (M75n) | 4C/8T | 3.25 GHz (no boost) | 7nm Zen+ | AMD-V, IOMMU |
| **Intel i5-9500T** | 6C/6T | 2.2-3.7 GHz | 14nm++ Coffee Lake | VT-x, VT-d |
| **Intel i5-10500T** | 6C/12T | 2.3-3.8 GHz | 14nm Comet Lake | VT-x, VT-d |

### Hyperthreading Impact for Virtualization

**VM Capacity Analysis:**
```bash
AMD V1756B:  4 physical cores  →  8 vCPUs max  →  2-3 VMs possible
i5-9500T:    6 physical cores  →  6 vCPUs max  →  2-3 VMs possible  
i5-10500T:   6 physical cores  → 12 vCPUs max  →  4-6 VMs possible
```

**Kubernetes Scheduling Benefits:**
- **Without HT (9500T)**: Pod scheduling limited by physical core count
- **With HT (10500T)**: 2x more scheduling slots, better container density
- **AMD (V1756B)**: Good threading but fewer total cores

## 🎯 Decision: Intel i5-10500T

**Why 10500T wins for K3s clusters:**

✅ **Hyperthreading Advantage** - 12 threads vs 6 enables higher VM density  
✅ **Proven Virtualization** - Mature VT-x/VT-d support in Proxmox  
✅ **Container Optimization** - Better performance with mixed CPU/IO workloads  
✅ **Future-Proof** - More headroom for cluster growth

> 🤓 *"The difference between 6 and 12 threads isn't just mathematical - it's the difference between a functional lab and a comfortable learning environment."*

---

## 💾 Memory Configuration Analysis

### 16GB vs 32GB Trade-offs

| Aspect | 16GB Configuration | 32GB Configuration |
|--------|-------------------|-------------------|
| **VM Sizing** | 2-3 VMs @ 4-6GB each | 3-4 VMs @ 6-8GB each |
| **Pod Capacity** | ~150-200 total pods | ~250-300 total pods |
| **Monitoring Stack** | Basic metrics only | Full Prometheus/Grafana |
| **CI/CD Capability** | Limited runners | Multiple Jenkins agents |
| **Cost Difference** | Base price | +€100 per node (~€300 total) |
| **Upgrade Path** | ✅ SODIMM upgradeable | Final configuration |

### Pragmatic Approach: Start with 16GB

**Rationale:**
- 16GB sufficient for initial K3s cluster setup and learning
- Memory easily upgradeable via SODIMM slots when needed
- €300 savings can fund other lab components initially
- Upgrade when workload requirements become clear

> 💡 *"Perfect is the enemy of good - and 16GB is definitely good enough to start serious learning."*

---

## 📊 Cluster Capacity Planning

### Node Configuration: i5-10500T + 16GB (Upgradeable)

#### Lab-Optimized VM Allocation (1.5x CPU Overcommit)

| Node | VM Type | vCPU | RAM | Role | Purpose |
|------|---------|------|-----|------|---------|
| **Node 1** | Control+Worker | 6 | 6GB | K3s Master | HA control plane |
| | Worker | 6 | 8GB | K3s Worker | Application workloads |
| **Node 2** | Control+Worker | 6 | 6GB | K3s Master | HA control plane |
| | Worker | 6 | 8GB | K3s Worker | Application workloads |
| **Node 3** | Control+Worker | 6 | 6GB | K3s Master | HA control plane |
| | Worker | 6 | 8GB | K3s Worker | Application workloads |

**Cluster Totals:**
- **Physical**: 18 cores, 36 threads, 48GB RAM
- **Virtual**: 36 vCPU (1.5x overcommit), 42GB allocated
- **VMs**: 6 total (3 Control+Worker, 3 Pure Worker)

#### Aggressive Lab Setup (2x CPU Overcommit)

| Node | VM Type | vCPU | RAM | Purpose |
|------|---------|------|-----|---------|
| **Node 1** | Control+Worker | 8 | 5GB | Primary K3s control |
| | Worker | 8 | 5GB | High-performance worker |
| | Testing | 8 | 4GB | Experiments/CI |
| **Node 2** | Control+Worker | 8 | 5GB | HA control plane |
| | Worker | 8 | 5GB | High-performance worker |
| | Testing | 8 | 4GB | Development env |
| **Node 3** | Control+Worker | 8 | 5GB | HA control plane |
| | Worker | 8 | 5GB | High-performance worker |
| | Testing | 8 | 4GB | Load testing |

**Cluster Totals:**
- **Virtual**: 72 vCPU (2x overcommit), 42GB allocated  
- **VMs**: 9 total (3 Control+Worker, 3 Worker, 3 Testing)
- **Pod Capacity**: ~200-250 pods across cluster

### K3s Node Resource Allocation

#### Control+Worker Node (6-8 vCPU, 5-6GB)
```bash
System Overhead:     ~1GB RAM, 0.8 vCPU
K3s Control Plane:   ~750MB RAM, 0.5 vCPU  
Available for Pods:  ~4GB RAM, 5-7 vCPU
Estimated Pods:      ~20-25 pods per node
```

#### Pure Worker Node (6-8 vCPU, 5-8GB)
```bash
System Overhead:     ~1GB RAM, 0.5 vCPU
K3s Worker Services: ~300MB RAM, 0.3 vCPU
Available for Pods:  ~4-6GB RAM, 5-7 vCPU  
Estimated Pods:      ~25-35 pods per node
```

### Realistic Lab Workload Distribution

**Control+Worker Nodes (System Services):**
- CoreDNS, Metrics Server, Ingress Controller
- Monitoring components (Node Exporter, etc.)
- Cluster management tools

**Pure Worker Nodes (Application Workloads):**
- Microservices and applications
- Databases and stateful workloads  
- CI/CD runners and build agents

---

## 🎯 Final Configuration

### Recommended Starting Point
- **CPU**: i5-10500T (6C/12T) - non-negotiable for Hyperthreading advantage
- **RAM**: 16GB or 32GB - depending on availability and pricing opportunities
- **Platform Preference**: Dell OptiPlex (proven experience) but open to HP EliteDesk, Lenovo ThinkCentre, or Fujitsu Esprimo
- **Virtualization**: Proxmox with 1.5-2x CPU overcommit
- **Cluster**: 6-9 VMs running K3s in HA configuration

### Expected Capabilities
- **Pod Capacity**: 150-250 pods total across cluster
- **Learning Scenarios**: Basic to intermediate Kubernetes workloads
- **Monitoring**: Essential metrics and logging
- **Growth**: Easily expandable with memory upgrades

> 🚀 *"This configuration provides the sweet spot between learning capability and cost efficiency - with a clear upgrade path when ambitions grow."*

---

**Bottom Line**: i5-10500T + 16GB gives excellent learning foundation with room to grow, while Hyperthreading provides the threading density essential for realistic container workloads.