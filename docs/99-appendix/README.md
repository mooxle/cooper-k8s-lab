# Cooper'n'80s - Enterprise Kubernetes Homelab

<p align="center">
  <img src="assets/banner.png" alt="Cooper'n'80s Banner" width="800"/>
</p>

> Enterprise-grade Kubernetes lab for hands-on architectural learning

---

### 📺 Latest Episodes
**[S01E04 - The Infrastructure Revolution](docs/99-appendix/project-journal.md#s01e04---the-infrastructure-revolution)** *(Aug 20)* - Enterprise platform deployment & Vault integration  
**[S01E03 - The Assembly Protocol](docs/99-appendix/project-journal.md#s01e03---the-assembly-protocol)** *(Aug 19)* - Frame assembly & equipment delivery  
**[S01E02 - The Great Restructuring](docs/99-appendix/project-journal.md#s01e02---the-great-restructuring)** *(Aug 18)* - Repository engineering & frame completion

**[📖 Full Project Journal](docs/99-appendix/project-journal.md)** - *Complete episode guide with Cooper'scher commentary*

---

## 🎯 Project Overview

Enterprise Architect's learning laboratory combining **theoretical knowledge** with **practical implementation**. Built to bridge the gap between architecture decisions and operational reality.

**Current Focus**: Enterprise infrastructure platform with unified Docker deployment  
**Learning Goal**: Master enterprise infrastructure patterns through hands-on experience  
**Approach**: Scientific method applied to infrastructure architecture

### 🏗️ Current Build Progress

<p align="center">
  <img src="docs/03-hardware/assembly/photos/20_08_rack_front.png" alt="Cooper'n'80s Enterprise Rack" width="600"/>
  <br>
  <em>Complete enterprise infrastructure platform with integrated branding and network equipment</em>
</p>

## 📚 Documentation

| Section | Focus | Key Documents |
|---------|-------|---------------|
| **[🎯 Vision](docs/01-vision/)** | Why & What | [Architecture](docs/01-vision/architecture.md) • [Learning Goals](docs/01-vision/learning-goals.md) |
| **[📐 Design](docs/02-design/)** | Architecture Decisions | [Network Topology](docs/02-design/network-topology.md) • [K8s Strategy](docs/02-design/kubernetes-strategy.md) |
| **[🔧 Hardware](docs/03-hardware/)** | Physical Components | [Components Overview](docs/03-hardware/components/) • [Assembly Progress](docs/03-hardware/assembly/) • [Shopping List](docs/03-hardware/shopping-list.md) |
| **[⚙️ Implementation](docs/04-implementation/)** | Code & Configuration | *Coming Soon* - Terraform, Ansible, K8s manifests |
| **[🔍 Operations](docs/05-operations/)** | Running the Lab | *Coming Soon* - Monitoring, troubleshooting, maintenance |
| **[🗄️ CMDB](docs/06-cmdb/)** | Configuration Management | [Dual Repository Strategy](docs/06-cmdb/) • [Network Templates](docs/06-cmdb/templates/) |

## 🚀 Quick Start

**New to the project?** → [Getting Started Guide](GETTING_STARTED.md)  
**Want current status?** → Check the [progress indicators](#current-status) below  
**Looking for specific info?** → Use the [documentation sections](#documentation) above

## 📊 Current Status

```
🖨️ Hardware     █████████▓ 🟢 Rack complete, Mini PC mounting pending
🏗️ Infrastructure ████████████ 🟢 Enterprise platform operational
🔐 Secrets Mgmt ████████████ 🟢 HashiCorp Vault deployed
🌐 Networking   ████████▓▓ 🟡 Equipment mounted, connectivity testing
⚙️ Software     ███░░░░░░░ 🟡 Infrastructure as Code development
🚀 K8s Cluster  ██░░░░░░░░ ⚪ Awaiting Mini PC delivery
```

**Latest Progress**: Enterprise infrastructure platform with Vault secrets management operational  
**Next**: Mini PC delivery → rack integration → first Kubernetes cluster deployment

### 🎯 Recent Milestones
- ✅ **Enterprise Infrastructure**: Unified Docker platform with Forgejo + Vault
- ✅ **Secrets Management**: HashiCorp Vault with production security patterns
- ✅ **Professional Assembly**: Complete 8U rack with integrated network equipment
- ✅ **Dual Repository Strategy**: Operational security with public learning templates
- 🟡 **Hardware Procurement**: Dell OptiPlex 3080 Micro (32GB) ordered

## 🏗️ Enterprise Infrastructure Platform

**Unified Stack Deployment**:
```yaml
services:
  forgejo:    # Git server with SSH access and private repositories
  vault:      # Enterprise secrets management with CLI integration
  # Network: cooper-infrastructure (service discovery)
```

**Operational Capabilities**:
- **Version Control**: Private git server (cooper-ops) + public documentation
- **Secrets Management**: Centralized credential storage with enterprise security
- **Infrastructure Automation**: Modern Docker orchestration with systematic deployment
- **Backup & Recovery**: 24-hour LXC backup to 3 geographic locations

## 🎯 Core Architecture

**Two-Path Strategy**: Experience different infrastructure paradigms

- **Path A**: Kubernetes ON Virtualization (Proxmox + K3s)
- **Path B**: Virtualization IN Kubernetes (OKD + KubeVirt)

**Hardware**: 3x Mini PCs (i5-10500T, 32GB RAM) in custom 3D-printed 8U rack  
**Network**: Managed L2 switch with VLAN segmentation and keystone patch panel  
**Automation**: Full Infrastructure as Code with Vault integration

## 🛠️ Key Technologies

**Infrastructure**: Proxmox, OKD/OpenShift, Terraform, Ansible  
**Kubernetes**: K3s, KubeVirt, Calico/Flannel networking  
**Secrets**: HashiCorp Vault with enterprise security patterns  
**Monitoring**: Prometheus, Grafana, AlertManager  
**Security**: Network segmentation, RBAC, encrypted storage

## 🧪 Learning Philosophy

> *"In theory, theory and practice are the same. In practice, they are not."*

This lab applies the **scientific method** to infrastructure:
1. **Hypothesis** - Architectural decisions based on enterprise patterns
2. **Experiment** - Real-world implementation and validation
3. **Observation** - Document what actually works vs theoretical assumptions
4. **Iteration** - Refine based on evidence and operational experience

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
  📝 <strong>Visual Studio Code</strong> • 🤖 <strong>Claude AI</strong> • 🌐 <strong>MakerWorld</strong><br>
  🔐 <strong>HashiCorp Vault</strong> • 🐙 <strong>Forgejo Git</strong> • 🏗️ <strong>Docker Compose</strong>
</p>

---

<p align="center">
  <em>"The most remarkable aspect of systematic engineering is how it naturally evolves into comprehensive enterprise infrastructure."</em><br>
  <strong>Current Version</strong>: v0.3.0 (Enterprise Infrastructure Platform)
</p>