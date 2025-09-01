# Cooper'n'80s - Enterprise Kubernetes Homelab

<p align="center">
  <img src="assets/banner.png" alt="Cooper'n'80s Banner" width="800"/>
</p>

> Enterprise-grade Kubernetes lab for hands-on architectural learning

<p align="center">
  <em>Hardware powered by</em>
  <a href="#-partners--acknowledgments">
    <img src="https://greendot.it/bilder/intern/shoplogo/greenDotITweb.png" alt="GreenDot IT" height="30" style="vertical-align: middle; margin-left: 8px;"/>
  </a>
</p>
---

### 📺 Latest Episodes
**[S01E11 - The VM Automation Paradigm](docs/99-appendix/project-journal.md#-episode-s01e11---the-VM-automation-paradigm)** *(Sep 01)* - VM Automation with Terraform 
**[S01E10 - The Vault Transit Paradigm](docs/99-appendix/project-journal.md#-episode-s01e10---the-vault-transit-paradigm)** *(Aug 30)* - Vault Transit integration with cross-node ZFS access and network stability engineering  
**[S01E09 - The Network Services Integration](docs/99-appendix/project-journal.md#-episode-s01e09---the-network-services-integration)** *(Aug 26)* - DNS/DHCP migration to 10.0.1.23 + EVPN/VXLAN + TPM-backed ZFS encryption  
**[S01E08 - The Storage & Overlay Paradigm](docs/99-appendix/project-journal.md#s01e08---the-storage--overlay-paradigm)** *(Aug 25)* - ZFS encrypted storage + VXLAN/EVPN overlay networking operational  
**[S01E07 - The Great Integration](docs/99-appendix/project-journal.md#-episode-s01e07---the-great-integration)** *(Aug 24)* - Complete infrastructure assembly with all Dell OptiPlex nodes integrated

## 🎯 Project Overview

Enterprise Architect's learning laboratory combining **theoretical knowledge** with **practical implementation**. Built to bridge the gap between architecture decisions and operational reality.

**Current Focus**: Vault Transit integration with cross-node ZFS access and enterprise automation 
**Learning Goal**: Master enterprise infrastructure patterns through hands-on experience  
**Approach**: Scientific method applied to infrastructure architecture  
**Hardware Platform**: Dell OptiPlex infrastructure from [**GreenDot IT**](https://greendot.it) <a href="#-partners--acknowledgments"><img src="https://greendot.it/bilder/intern/shoplogo/greenDotITweb.png" alt="GreenDot IT" height="16" style="vertical-align: middle;"/></a>

### 🏗️ Current Build Progress

<p align="center">
  <img src="docs/03-hardware/assembly/photos/25_08_rack_front.png" alt="Cooper'n'80s Complete Infrastructure Platform" width="600"/>
  <br>
  <em>Complete Cooper'n'80s infrastructure platform with three Dell OptiPlex 3080 Micro nodes, enterprise networking, and production-ready automation</em>
</p>

## 📚 Documentation

| Section | Focus | Key Documents |
|---------|-------|---------------|
| **[🎯 Vision](docs/01-vision/)** | Why & What | [Architecture](docs/01-vision/architecture.md) • [Learning Goals](docs/01-vision/learning-goals.md) |
| **[📐 Design](docs/02-design/)** | Architecture Decisions | [Network Topology](docs/02-design/network-topology.md) • [Network Services](docs/02-design/network-services.md) • [K8s Strategy](docs/02-design/kubernetes-strategy.md) • [Capability Model](docs/02-design/capability-model.md) |
| **[🔧 Hardware](docs/03-hardware/)** | Physical Components | [Components Overview](docs/03-hardware/components/) • [Assembly Progress](docs/03-hardware/assembly/) • [Shopping List](docs/03-hardware/shopping-list.md) |
| **[⚙️ Implementation](docs/04-implementation/)** | Code & Configuration | [Path A: Proxmox + K3s](docs/04-implementation/path-a-proxmox/) • [ZFS Encrypted Storage](docs/04-implementation/path-a-proxmox/README.md#enterprise-foundation-implementation-new) • [VXLAN/EVPN Networking](docs/02-design/network-topology.md#overlay-network-architecture-vxlanevpn) |
| **[🔍 Operations](docs/05-operations/)** | Running the Lab | [Network Services Operations](docs/05-operations/network-services.md) • Monitoring & troubleshooting |
| **[🗄️ CMDB](docs/06-cmdb/)** | Configuration Management | [Dual Repository Strategy](docs/06-cmdb/) • [Network Templates](docs/06-cmdb/templates/) |

## 🚀 Quick Start

**New to the project?** → [Getting Started Guide](GETTING_STARTED.md)  
**Want current status?** → Check the [progress indicators](#current-status) below  
**Looking for specific info?** → Use the [documentation sections](#documentation) above

## 🔗 Quick Reference

### 🏗️ Implementation Status
- **ZFS Encrypted Storage**: [Technical Details](docs/04-implementation/path-a-proxmox/README.md#enterprise-foundation-implementation-new) • [Episode Documentation](docs/99-appendix/project-journal.md#s01e08---the-storage--overlay-paradigm)
- **VXLAN/EVPN Networking**: [Network Architecture](docs/02-design/network-topology.md#overlay-network-architecture-vxlanevpn) • [BGP Configuration Details](docs/99-appendix/project-journal.md#part-2-vxlanevpn-networking)
- **Proxmox Automation**: [45-minute Pipeline](docs/04-implementation/path-a-proxmox/automated-deployment.md) • [Episode S01E06](docs/99-appendix/project-journal.md#s01e06---the-proxmox-automation-revolution)

### 📚 Learning Resources
- **Enterprise Patterns**: [Architecture Philosophy](docs/01-vision/architecture.md#storage--network-foundation-architecture) 
- **Lessons Learned**: [Storage & Network](docs/99-appendix/lessons-learned.md#storage--network-implementation) • [All Technical Discoveries](docs/99-appendix/lessons-learned.md)
- **Project Journal**: [Complete Episode Guide](docs/99-appendix/project-journal.md#episode-guide)


| Category | Progress | Next Milestone |
|----------|----------|----------------|
| **🖥️ Hardware** | ████████████ 100% | Mini PCs operational with Proxmox + VMs |
| **🌐 Network Services** | ████████████ 100% | Multi-layer routing + DHCP relay operational |
| **🏗️ Infrastructure** | ████████████ 100% | VM automation pipeline complete |
| **📚 Documentation** | ████████████ 100% | K3s HA deployment procedures |
| **🚀 VM Platform** | ████████████ 100% | Terraform lifecycle management |
| **🔐 Security Framework** | ████████████ 100% | SSH keys + cloud-init + DNS integration |

### Current Status

🚀 **VM Automation**: ✅ **OPERATIONAL** - Complete Terraform lifecycle management
- VM creation, configuration, and destruction fully automated
- Template-driven deployment across all Proxmox nodes  
- Cloud-init integration with SSH keys and guest tools
- Network integration with cooper.lab DNS registration
- Multi-layer routing: Fritz!Box → D-Link → VXLAN overlay

🌐 **Network Services**: ✅ **OPERATIONAL** - Complete DNS/DHCP stack at 10.0.1.23
- PowerDNS authoritative + recursor with cooper.lab domain
- Kea DHCP4 with dynamic DNS registration  
- PowerDNS Admin interface at http://10.0.1.23:8082
- Pi-hole integration with conditional forwarding

🔗 **VXLAN Overlay**: ✅ **OPERATIONAL** - Enterprise networking patterns
- EVPN/VXLAN mesh between all Proxmox nodes (vxlan100)
- Tenant network isolation (10.0.10.0/24 via vmbr1)
- Management network (10.0.1.0/24 via vmbr0) with static IPs
- Full inter-node connectivity and VM networking ready

🔐 **Storage Security**: ✅ **HARDENED** - TPM-backed ZFS encryption
- Native ZFS encryption (AES-256-GCM) on cooper-zfs pool
- TPM2 hardware-backed key management with auto-unlock
- Systemd integration for seamless boot process
- Key rotation strategy for both TPM objects and dataset keys

🖥️ **Infrastructure**: ✅ **COMPLETE** - Production-ready platform
- 3x Dell OptiPlex 3080 Micro (32GB RAM each) in custom 8U rack
- Proxmox VE cluster with automated deployment pipeline  
- SSH-only access with Vault credential management
- Complete monitoring and operational procedures

🚀 **Kubernetes Foundation**: ✅ **READY** - Enterprise infrastructure prepared
- Service discovery via cooper.lab DNS domain
- Network segmentation with VXLAN overlay networking
- Automated node provisioning and configuration management
- Security foundation with encrypted storage and hardened access


### 🎯 Recent Milestones
- ✅ **ZFS Encrypted Storage**: 1TB+ encrypted storage with Vault-managed keys (AES-256-GCM)
- ✅ **VXLAN + EVPN Networking**: BGP EVPN control plane with VNI 100 overlay (AS 65001)
- ✅ **Proxmox Automation Pipeline**: 45-minute bare-metal → production-ready nodes
- ✅ **Enterprise Security**: Vault integration with dynamic SSH keys and automated hardening  
- ✅ **Infrastructure as Code**: Complete Terraform + Ansible + Official Proxmox tools
- ✅ **Network Services Foundation**: Enterprise DNS/DHCP stack with PowerDNS + Kea DHCP
- ✅ **Service Discovery**: cooper.lab domain with automatic device registration
- ✅ **Professional Assembly**: Complete 8U rack with integrated network equipment
- ✅ **Hardware Procurement**: 3x Dell OptiPlex 3080 Micro (32GB RAM) delivered and integrated

## 🏗️ Enterprise Infrastructure Platform

**Unified Network Services Stack**:
```yaml
# Cooper DNS/DHCP Platform
services:
  powerdns-auth:     # Authoritative DNS for cooper.lab domain
  powerdns-recursor: # Recursive DNS with upstream forwarding
  kea-dhcp4:         # Dynamic IP assignment with DDNS
  kea-ddns:          # Automatic DNS record creation
  powerdns-admin:    # Web-based DNS management
  
# Cooper Enterprise Platform  
services:
  forgejo:           # Private Git server with SSH access
  vault:             # Enterprise secrets management
  # Network: cooper-infrastructure (unified service discovery)
```

**Operational Capabilities**:
- **Network Services**: Enterprise DNS/DHCP with automatic service discovery
- **Version Control**: Private git server (cooper-ops) + public documentation
- **Secrets Management**: Centralized credential storage with enterprise security
- **Service Discovery**: cooper.lab domain for unified lab networking
- **Infrastructure Automation**: Modern Docker orchestration with systematic deployment
- **Backup & Recovery**: 24-hour LXC backup to 3 geographic locations

## 🎯 Core Architecture

**Two-Path Strategy**: Experience different infrastructure paradigms

- **Path A**: Kubernetes ON Virtualization (Proxmox + K3s)
- **Path B**: Virtualization IN Kubernetes (OKD + KubeVirt)

**Hardware**: 3x Mini PCs (i5-10500T, 32GB RAM) in custom 3D-printed 8U rack  
**Network**: Managed L2 switch with VLAN segmentation, keystone patch panel, and enterprise DNS/DHCP  
**Service Discovery**: cooper.lab domain with automatic device and service registration  
**Automation**: Full Infrastructure as Code with Vault integration and network services foundation

## 🛠️ Key Technologies

**Network Services**: PowerDNS, Kea DHCP, Dynamic DNS with cooper.lab domain  
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
## 🤝 Partners & Acknowledgments

<p align="center">
  <strong>Cooper'n'80s is built with exceptional hardware and inspired by outstanding community contributions</strong>
</p>

### 🖥️ Hardware Partner
<p align="center">
  <a href="https://greendot.it" target="_blank">
    <img src="https://greendot.it/bilder/intern/shoplogo/greenDotITweb.png" alt="GreenDot IT" height="60"/>
  </a>
</p>

**Dell OptiPlex 3080 Micro nodes** sourced from **[GreenDot IT](https://greendot.it)** - professional business equipment supplier with excellent customer service and competitive enterprise pricing. The three i5-10500T systems with 32GB RAM each form the backbone of our infrastructure platform. **Vielen Dank** for the outstanding hardware and professional support!

### 🎨 Design Inspiration  
<p align="center">
  <a href="https://makerworld.com/de/@mklements" target="_blank">
    <img src="https://cdn.makerworld.com/model/us/0003/42/23/6c4f9b81c1ca3_680x680.png?x-oss-process=image/resize,w_680,h_680,m_fit" alt="MakerWorld Profile" height="60"/>
  </a>
</p>

**Rack design foundations** inspired by exceptional work from **[@mklements](https://makerworld.com/de/@mklements)** on MakerWorld. The original rack concepts provided the architectural starting point that evolved into our custom Cooper'n'80s branded infrastructure platform.

---

## 🛠️ Made With

<p align="center">
  <strong>Built with passion for precision and an unreasonable attention to detail</strong>
</p>

<p align="center">
  💻 <strong>Made with a Mac</strong> • 🖨️ <strong>Bambu Lab P1S</strong> + <strong>Bambu Studio</strong><br>
  📝 <strong>Visual Studio Code</strong> • 🤖 <strong>Claude AI</strong> • 🌐 <strong>MakerWorld</strong><br>
  🌐 <strong>PowerDNS + Kea DHCP</strong> • 🔐 <strong>HashiCorp Vault</strong> • 🐙 <strong>Forgejo Git</strong> • 🏗️ <strong>Docker Compose</strong>
</p>

---

<p align="center">
  <em>"The most elegant aspect of proper network infrastructure is how it becomes completely invisible when implemented correctly."</em><br>
  <strong>Current Version</strong>: v0.4.0 (Network Services Foundation)
</p>