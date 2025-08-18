# Cooper'n'80s - Enterprise Kubernetes Homelab

<p align="center">
  <img src="assets/cooper-n-80s_500px.png" alt="Cooper'n'80s Logo" width="300"/>
</p>

> Enterprise-grade Kubernetes lab for hands-on architectural learning

## 🎯 Project Overview

Enterprise Architect's learning laboratory combining **theoretical knowledge** with **practical implementation**. Built to bridge the gap between architecture decisions and operational reality.

**Current Focus**: 3D-printed 8U rack with 3-node Kubernetes cluster  
**Learning Goal**: Master enterprise infrastructure patterns through hands-on experience  
**Approach**: Scientific method applied to infrastructure architecture

## 📚 Documentation

| Section | Focus | Key Documents |
|---------|-------|---------------|
| **[🎯 Vision](docs/01-vision/)** | Why & What | [Architecture](docs/01-vision/architecture.md) • [Learning Goals](docs/01-vision/learning-goals.md) |
| **[📐 Design](docs/02-design/)** | Architecture Decisions | [Network Topology](docs/02-design/network-topology.md) • [K8s Strategy](docs/02-design/kubernetes-strategy.md) |
| **[🔧 Hardware](docs/03-hardware/)** | Physical Components | [Components](docs/03-hardware/components/) • [3D Printing](docs/03-hardware/assembly/3d-printing.md) • [Shopping List](docs/03-hardware/shopping-list.md) |
| **[⚙️ Implementation](docs/04-implementation/)** | Code & Configuration | [Path A: Proxmox](docs/04-implementation/path-a-proxmox/) • [Path B: OKD](docs/04-implementation/path-b-okd/) |
| **[🔍 Operations](docs/05-operations/)** | Running the Lab | [Monitoring](docs/05-operations/monitoring.md) • [Troubleshooting](docs/05-operations/troubleshooting.md) |

## 🚀 Quick Start

**New to the project?** → [Getting Started Guide](GETTING_STARTED.md)  
**Want current status?** → Check the [progress indicators](#current-status) below  
**Looking for specific info?** → Use the [documentation sections](#documentation) above

## 📊 Current Status

```
🖨️ Hardware     ████████░░ 🟡 3D printing in progress
🌐 Networking   ██████░░░░ 🟡 Equipment ordered, topology defined  
⚙️ Software     ██░░░░░░░░ ⚪ Pending hardware completion
🚀 K8s Cluster  ░░░░░░░░░░ ⚪ Awaiting infrastructure
```

**Next**: Complete rack assembly → Source Mini PCs → Deploy Proxmox

## 🎯 Core Architecture

**Two-Path Strategy**: Experience different infrastructure paradigms

- **Path A**: Kubernetes ON Virtualization (Proxmox + K3s)
- **Path B**: Virtualization IN Kubernetes (OKD + KubeVirt)

**Hardware**: 3x Mini PCs (i5-10500T) in custom 3D-printed 8U rack  
**Network**: Managed L2 switch with VLAN segmentation  
**Automation**: Full Infrastructure as Code approach

## 🛠️ Key Technologies

**Infrastructure**: Proxmox, OKD/OpenShift, Terraform, Ansible  
**Kubernetes**: K3s, KubeVirt, Calico/Flannel networking  
**Monitoring**: Prometheus, Grafana, AlertManager  
**Security**: HashiCorp Vault, network segmentation, RBAC

## 🧪 Learning Philosophy

> *"In theory, theory and practice are the same. In practice, they are not."*

This lab applies the **scientific method** to infrastructure:
1. **Hypothesis** - Architectural decisions based on theory
2. **Experiment** - Real-world implementation
3. **Observation** - Document what actually works
4. **Iteration** - Refine based on evidence

## 🤝 Contributing

**Found an issue?** Open a GitHub issue  
**Have suggestions?** Start a discussion  
**Want to share experience?** PRs welcome for lessons learned

## 📄 License

Documentation: MIT License  
3D Models: Creative Commons Attribution-ShareAlike 4.0

---

<p align="center">
  <em>"Bazinga! Now let's see if this actually works in practice."</em><br>
  <strong>Current Version</strong>: v0.2.0 (Restructured for Navigation)
</p>