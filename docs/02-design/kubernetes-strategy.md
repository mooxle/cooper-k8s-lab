# Kubernetes Platform Strategy

> Two-paradigm approach to modern infrastructure architecture

## 🎯 Strategic Overview

**Core Question**: Should virtualization be the foundation that runs Kubernetes, or should Kubernetes be the foundation that runs virtualization?

**Scientific Approach**: Implement both paradigms to experience fundamental architectural trade-offs firsthand through template-driven configuration enabling paradigm switching.

**Learning Goal**: Master enterprise infrastructure patterns through real-world validation of theoretical architectural decisions.

> *"This laboratory explores the most fundamental question in modern infrastructure - the relationship between virtualization and container orchestration platforms."*

## 🛤️ Two-Paradigm Architecture

### Path A: Kubernetes ON Virtualization
```
Hardware → Proxmox → VMs → K3s Cluster → Container Workloads
```

**Philosophy**: Virtualization provides stable infrastructure foundation
- **Management Approach**: Separate control planes (hypervisor + K8s)
- **Isolation Strategy**: Strong VM boundaries for multi-tenancy
- **Resource Model**: Hardware → VMs → Containers (layered abstraction)
- **Enterprise Pattern**: Traditional datacenter approach

**🚀 IMPLEMENTATION STATUS: OPERATIONAL**
- ✅ **Infrastructure Automation**: 45-minute bare-metal → SSH-accessible Proxmox nodes
- ✅ **Vault Integration**: Dynamic secrets with Ed25519 SSH keys per node
- ✅ **Security Hardening**: SSH-only access, fail2ban, automated monitoring
- ✅ **Network Integration**: Automatic registration in cooper.lab domain
- ✅ **Enterprise Patterns**: Complete Infrastructure as Code with Terraform + Ansible
- 🟡 **K3s Deployment**: Ready for cluster bootstrap on automated infrastructure

**Real-World Validation**: Hypothesis confirmed - traditional virtualization provides stable foundation with proven enterprise deployment patterns.

**Benefits**:
✅ **Proven approach** - Well-understood virtualization patterns  
✅ **Hardware abstraction** - VM portability and live migration  
✅ **Multi-tenant safe** - Strong isolation boundaries  
✅ **Flexible sizing** - Easy VM resource adjustment  
✅ **Recovery options** - VM snapshots and backups

**Learning Outcomes**:
- Traditional enterprise virtualization patterns
- Multi-layer resource management complexity
- VM-based cluster node lifecycle
- Storage and networking through hypervisor layer

### Path B: Virtualization IN Kubernetes
```
Hardware → OKD Baremetal → OKD Virtualization (KubeVirt)
```

**Philosophy**: Kubernetes orchestrates everything including VMs
- **Management Approach**: Unified control plane for all workloads
- **Isolation Strategy**: Kubernetes-native security and networking
- **Resource Model**: Hardware → Containers + VMs (unified orchestration)
- **Enterprise Pattern**: Cloud-native datacenter approach

**Benefits**:
✅ **Unified management** - Single control plane for VMs and containers  
✅ **Enterprise native** - OpenShift/OKD production patterns  
✅ **Advanced features** - Live migration, unified networking (OVN-K8s)  
✅ **Cloud-like experience** - Kubernetes-native virtualization  
✅ **Lower overhead** - No hypervisor layer between hardware and K8s

**Learning Outcomes**:
- Cloud-native virtualization with KubeVirt
- Enterprise Kubernetes platform (OpenShift/OKD)
- Unified container + VM orchestration
- Advanced networking (OVN-Kubernetes, service mesh)

## 📊 Resource Efficiency Analysis

### Path A Resource Overhead
```bash
Hardware → Proxmox (~1GB RAM, 0.5 CPU per node)
         → VM Guest OS (~1GB RAM, 0.3 CPU per VM)  
         → K3s Services (~800MB RAM, 0.4 CPU per node)

Total Overhead: ~40-50% of base hardware resources
Available for Workloads: ~50-60% of hardware capacity
```

### Path B Resource Overhead
```bash
Hardware → OKD Control Plane (~2GB RAM, 1 CPU per master)
         → KubeVirt Operator (~500MB RAM, 0.2 CPU overhead)
         → VM Workloads (direct allocation, minimal overhead)

Total Overhead: ~25-35% of base hardware resources
Available for Workloads: ~65-75% of hardware capacity
```

### Comparative Analysis Target
**Hypothesis**: Path B should provide ~15-20% better resource efficiency  
**Validation**: Measure actual overhead with identical workloads  
**Variables**: Memory usage, CPU utilization, storage efficiency, network performance

## 🔧 Distribution Selection Strategy

### Path A: K3s on Proxmox VMs

**Why K3s**:
- **Minimal overhead** on VM resources
- **Excellent automation** support for scripted deployment
- **Battle-tested** in edge and resource-constrained scenarios
- **Simple networking** works well with VM-to-VM connectivity

**VM Resource Allocation**:
```
Control+Worker Node: 6 vCPU, 6GB RAM
Pure Worker Node:    6 vCPU, 8GB RAM
Cluster Capacity:    36 vCPU, 42GB RAM (6 VMs total)
Pod Estimate:        150-200 pods across cluster
```

**Alternative Considered**: MicroK8s
- Higher overhead than K3s
- Snap dependency complicates automation
- Good Ubuntu integration but less resource-efficient

### Path B: Native OKD with KubeVirt

**Why OKD**:
- **Enterprise OpenShift features** without licensing costs
- **Native KubeVirt integration** for unified VM/container management
- **Advanced networking** with OVN-Kubernetes
- **Multi-tenancy** and enterprise security features

**Hardware Requirements**:
```
Master Nodes: 4+ vCPU, 16GB+ RAM (bootstrap requirements)
Worker Nodes: 2+ vCPU, 8GB+ RAM (workload capacity)
Storage:      120GB+ per node (CoreOS + container images)
```

**Enterprise Features**:
- Built-in container registry
- Integrated monitoring (Prometheus/Grafana)
- Developer console and CLI tools
- Enterprise security and RBAC

## 🔄 Implementation Strategy

### Scientific Methodology

**Hypothesis-Driven Deployment**:
1. **Hypothesis**: "Path A provides better isolation but Path B offers superior resource efficiency"
2. **Experiment**: Deploy identical workloads on both architectures
3. **Observation**: Measure performance, resource usage, operational complexity
4. **Documentation**: Capture quantitative differences and qualitative experiences

### Template-Driven Configuration

**Infrastructure as Code Approach**:
```bash
cooper-n-80s/
├── src/terraform/
│   ├── path-a-proxmox/     # Proxmox + VM provisioning
│   └── path-b-okd/         # OKD baremetal bootstrap
├── src/ansible/
│   ├── k3s-cluster/        # K3s deployment and config
│   └── okd-setup/          # OKD post-install configuration
└── src/kubernetes/
    ├── workloads/          # Test applications (identical)
    ├── monitoring/         # Observability stack
    └── comparison/         # Benchmarking tools
```

**Paradigm Switching Workflow**:
```
Deploy Path A → Run workloads → Measure performance → 
Document findings → Wipe environment → Deploy Path B → 
Compare results → Architectural insights
```

## 📈 Cluster Capacity Planning

### Lab-Optimized Resource Allocation

**Path A: VM-Based Nodes**
```
Physical Resources:  18 cores, 36 threads, 48GB RAM (3 Mini PCs)
VM Overhead:        ~12GB RAM, ~6 vCPU (Proxmox + Guest OS)
K3s Overhead:       ~3GB RAM, ~3 vCPU (K3s services)
Available:          ~33GB RAM, ~27 vCPU for workloads
Pod Density:        150-200 pods (limited by RAM per node)
```

**Path B: Baremetal Nodes**
```
Physical Resources:  18 cores, 36 threads, 48GB RAM (3 Mini PCs)
OKD Overhead:       ~6GB RAM, ~3 vCPU (control plane services)
Available:          ~42GB RAM, ~33 vCPU for workloads  
Pod Density:        200-250 pods (better resource utilization)
```

### Workload Distribution Strategy

**Control Plane** (HA across 3 nodes):
- etcd cluster (distributed)
- Kubernetes API servers
- Controller managers and schedulers
- Ingress controllers

**Worker Capacity**:
- Application pods and services
- Monitoring and logging stack
- CI/CD runners and build agents
- Storage and database workloads

## 🎯 Learning Validation Framework

### Technical Metrics
- **Resource Efficiency**: Overhead comparison between paradigms
- **Performance**: Workload performance under both architectures
- **Operational Complexity**: Deployment, upgrade, and troubleshooting effort
- **Feature Completeness**: Available enterprise features and capabilities

### Experiential Learning
- **Day-1 Operations**: Initial deployment and configuration complexity
- **Day-2 Operations**: Ongoing management, updates, and maintenance
- **Troubleshooting**: Multi-layer debugging approaches and tools
- **Scaling**: Adding nodes, workloads, and feature expansion

### Architectural Insights
- **Trade-off Analysis**: Resource efficiency vs operational simplicity
- **Use Case Fit**: When to choose each paradigm in enterprise contexts
- **Migration Complexity**: Effort required to transform between approaches
- **Future Roadmap**: Technology evolution and convergence trends

## 🔬 Experimental Design

### Controlled Variables
- **Hardware**: Identical Mini PC specifications across tests
- **Workloads**: Same applications, resource requests, and scaling
- **Network**: Consistent connectivity and performance characteristics
- **Storage**: Equivalent persistence and I/O patterns

### Measured Variables
- **Resource Utilization**: CPU, memory, storage, network consumption
- **Response Times**: Application performance and API latency
- **Deployment Time**: From bare metal to running workloads
- **Operational Overhead**: Management tasks and troubleshooting effort

### Success Criteria
- **Quantitative**: 15%+ resource efficiency difference validates hypothesis
- **Qualitative**: Clear operational preference based on experience
- **Educational**: Documented insights transferable to enterprise decisions
- **Practical**: Reproducible deployment templates for either paradigm

## 📚 Integration Points

### Network Architecture
- **[Network Topology](network-topology.md)**: Physical and logical connectivity
- **CNI Selection**: Calico (Path A) vs OVN-Kubernetes (Path B)
- **Service Mesh**: Istio or Linkerd integration considerations

### Hardware Foundation
- **[Mini PC Specifications](../03-hardware/components/mini-pcs.md)**: Resource capacity planning
- **Storage Strategy**: Local NVMe utilization and future NAS integration
- **Power and Cooling**: Thermal considerations for both paradigms

### Operations Integration
- **Monitoring**: Prometheus/Grafana deployment on both platforms
- **Backup**: VM snapshots (Path A) vs etcd/persistent volume backup (Path B)
- **Disaster Recovery**: Platform-specific recovery procedures

---

**Strategic Insight**: This lab becomes a paradigm comparison laboratory where the fundamental infrastructure philosophy is the primary learning objective, validated through scientific methodology applied to enterprise architecture decisions.