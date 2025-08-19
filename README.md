# Cooper'n'80s - Enterprise Kubernetes Homelab

<p align="center">
  <img src="assets/banner.png" alt="Cooper'n'80s Banner" width="800"/>
</p>

> Enterprise-grade Kubernetes lab for hands-on architectural learning

---

### 📺 Latest Episodes
**[S01E03 - The Assembly Protocol](docs/99-appendix/project-journal.md#s01e03---the-assembly-protocol)** *(Aug 19)* - Frame assembly & equipment delivery  
**[S01E02 - The Great Restructuring](docs/99-appendix/project-journal.md#s01e02---the-great-restructuring)** *(Aug 18)* - Repository engineering & frame completion  

**[📖 Full Project Journal](docs/99-appendix/project-journal.md)** - *Complete episode guide with Cooper'scher commentary*

---

## 🎯 Project Overview

Enterprise Architect's learning laboratory combining **theoretical knowledge** with **practical implementation**. Built to bridge the gap between architecture decisions and operational reality.

**Current Focus**: 3D-printed 8U rack with 3-node Kubernetes cluster  
**Learning Goal**: Master enterprise infrastructure patterns through hands-on experience  
**Approach**: Scientific method applied to infrastructure architecture

### 🏗️ Current Build Progress

<p align="center">
  <img src="docs/03-hardware/assembly/photos/assembly_03.png" alt="Assembled Cooper'n'80s Rack Frame" width="600"/>
  <br>
  <em>Complete 8U rack frame with Cooper'n'80s branding - ready for equipment integration</em>
</p>

## 📚 Documentation

| Section | Focus | Key Documents |
|---------|-------|---------------|
| **[🎯 Vision](docs/01-vision/)** | Why & What | [Architecture](docs/01-vision/architecture.md) • [Learning Goals](docs/01-vision/learning-goals.md) |
| **[📐 Design](docs/02-design/)** | Architecture Decisions | [Network Topology](docs/02-design/network-topology.md) • [K8s Strategy](docs/02-design/kubernetes-strategy.md) |
| **[🔧 Hardware](docs/03-hardware/)** | Physical Components | [Components Overview](docs/03-hardware/components/) • [Assembly Progress](docs/03-hardware/assembly/) • [Shopping List](docs/03-hardware/shopping-list.md) |
| **[⚙️ Implementation](docs/04-implementation/)** | Code & Configuration | *Coming Soon* - Terraform, Ansible, K8s manifests |
| **[🔍 Operations](docs/05-operations/)** | Running the Lab | *Coming Soon* - Monitoring, troubleshooting, maintenance |

## 🚀 Quick Start

**New to the project?** → [Getting Started Guide](GETTING_STARTED.md)  
**Want current status?** → Check the [progress indicators](#current-status) below  
**Looking for specific info?** → Use the [documentation sections](#documentation) above

## 📊 Current Status

```
🖨️ Hardware     ████████▓▓ 🟡 Frame assembled, equipment mounting ready
🌐 Networking   ████░░░░░░ 🟡 Equipment ordered, topology defined  
⚙️ Software     ██░░░░░░░░ ⚪ Pending hardware completion
🚀 K8s Cluster  ░░░░░░░░░░ ⚪ Awaiting infrastructure
```

**Latest Progress**: Complete 8U rack frame assembled with Cooper'n'80s branding  
**Next**: Equipment mounting brackets → Switch installation → Mini PC procurement

### 🎯 Recent Milestones
- ✅ **Repository Restructure**: Professional documentation hierarchy
- ✅ **3D Printing Complete**: Base frame, extensions, and custom branding elements  
- 🟡 **Network Equipment**: D-Link switch and patch cables ordered
- ✅ **Frame Assembly Complete**: Professional 8U structure with integrated branding


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

## 🛠️ Made With

<p align="center">
  <strong>Built with passion for precision and an unreasonable attention to detail</strong>
</p>

<p align="center">
  💻 <strong>Made with a Mac</strong> • 🖨️ <strong>Bambu Lab P1S</strong> + <strong>Bambu Studio</strong><br>
  📝 <strong>Visual Studio Code</strong> • 🤖 <strong>Claude AI</strong> • 🌐 <strong>MakerWorld</strong>
</p>

---

<p align="center">
  <em>"Bazinga! Now let's see if this actually works in practice."</em><br>
  <strong>Current Version</strong>: v0.2.0 (Restructured for Navigation)
</p>