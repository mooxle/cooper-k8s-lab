# Cooper'n'80s Kubernetes Homelab

<p align="center">
  <img src="assets/cooper-n-80s_500px.png" alt="Cooper'n'80s Logo" width="300"/>
</p>

> *"In theory, theory and practice are the same. In practice, they are not."* - Attributed to Einstein (and every homelab builder ever)

## 🚀 Current Status

```
🖨️ 3D Printing    ████████░░ 🟡 Active     (Base rack printing)
🔧 Hardware       ██░░░░░░░░ 🔴 Planning   (Sourcing components)  
💾 Proxmox        ░░░░░░░░░░ ⚪ Waiting    (Hardware dependent)
🌐 Networking     ░░░░░░░░░░ ⚪ Waiting    (Proxmox dependent)
🚀 Kubernetes     ░░░░░░░░░░ ⚪ Waiting    (Infrastructure dependent)
🔒 Security       ░░░░░░░░░░ ⚪ Waiting    (Base platform dependent)
```

**Status Legend:**
🟢 Complete • 🟡 Active • 🔴 Planning • ⚪ Waiting

## 🎯 Project Concept

### Background & Motivation

As an **Enterprise Architect** responsible for Hybrid Private Cloud Architecture in my professional life, I have extensive knowledge and experience with cloud infrastructure, automation, networking, and compute technologies. However, I want to gain **hands-on practical experience** to complement my theoretical understanding.

**This isn't about becoming a professional cloud platform engineer** - others do that much better than I could. Instead, my goal is to gain enough practical experience to:
- **Demonstrate understanding** through actual implementation
- **Gain real-world insights** for innovative solutions
- **Bridge the gap** between architecture theory and operational reality
- **Explore GenAI collaboration** - understanding what works well and what doesn't in technical projects

> 🤖 *"I'm not replacing human intelligence with artificial intelligence. I'm augmenting my theoretical knowledge with AI assistance to achieve practical perfection."*

### Vision: Enterprise-Grade Homelab

Create a **near enterprise-grade lab setup** that replicates enterprise environments at small scale. 

> 🤓 *"If I'm going to have a lab, it's going to be the best lab possible - theoretically and practically perfect."*

**CIA Triad Compliance** *(because even a homelab needs standards):*
- **Confidentiality** - Encryption at rest, access control, secrets management
- **Integrity** - Immutable infrastructure, audit trails, change control  
- **Availability** - High availability, disaster recovery, monitoring

> 📊 *"I don't have OCD. I have a condition called Obsessive Compulsive Order - which is completely different."*

**Full Lifecycle Automation** *(manual processes are for theoretical physicists):*
- **Deployment** - Infrastructure as Code (Terraform, Ansible)
- **Change Management** - GitOps workflows, CI/CD pipelines
- **Upgrades** - Automated updates with rollback capabilities
- **Decommissioning** - Clean resource cleanup and documentation

**Multi-Layer Management** *(like a well-organized periodic table):*
- **Physical components** - Hardware nodes, networking, storage
- **Virtual infrastructure** - VMs, container orchestration, networking overlays
- **Application layer** - Services, databases, monitoring stack

> 🧪 *"I'm not crazy; my mother had me tested. I'm just building enterprise-grade infrastructure in my spare time."*

### Technology Learning Path

This project serves as my iterative learning platform for enterprise technologies. 

> 🔬 *"I approach learning like I approach everything else - with methodical precision and an unreasonable attention to detail."*

**Core Technologies:**
- **Infrastructure as Code** - Terraform for declarative infrastructure
- **Configuration Management** - Ansible for system automation  
- **Secrets Management** - HashiCorp Vault for enterprise-grade security
- **GitOps** - Git-based workflows and CI/CD pipelines
- **Container Orchestration** - Kubernetes at production scale
- **Observability** - Monitoring, logging, and alerting

> 🎭 *"The best thing about this project is that I get to be wrong professionally, learn from it, and document every mistake for posterity."*

**The Master Plan** *(subject to revision, like any good scientific hypothesis):*
- **Self-printed 10-inch rack** - Custom designed for my specific needs
- **3 physical nodes** - Mini PCs providing the compute foundation  
- **Proxmox as baseline platform** - Virtualization layer for flexibility
- **Kubernetes nodes as VMs** - Container orchestration on virtual machines
- **Underlay and overlay networking** - Proper network segmentation and routing
- **Security** - Access control, encryption at rest, and secrets management

## 🧪 Learning Approach

This is fundamentally a **learning project**. I'm using this repository to:

- Document my thought process and decision-making
- Track what works and what doesn't
- Build knowledge step by step
- Create a reference for future me
- Share discoveries with the community

**Scientific Method Applied:**
1. **Hypothesis** - What I think will work
2. **Experiment** - Implementation and testing  
3. **Observation** - What actually happened
4. **Documentation** - Lessons learned for next iteration

*"The best thing about being a scientist is that you get to be wrong professionally."* - Probably something Sheldon would say

## 📚 Documentation Structure

The project will grow organically. Current areas:

- **[Hardware](docs/hardware/)** - Physical components and 3D printed rack
  - [3D Printed Rack](docs/hardware/3d-printed-rack.md) - Custom Lab-Rax build with Bambu P1S
- **[Learning](docs/learning/)** - Tools and workflow evolution
  - [Toolset Evolution](docs/learning/toolset-evolution.md) - Development tools and setup progression
  - [Claude AI Workflow](docs/learning/claude-workflow.md) - Human-AI collaboration process
- **Infrastructure** - Proxmox setup and networking
- **Kubernetes** - Container orchestration layer
- **Automation** - Making it all reproducible
- **Lessons Learned** - What I discovered along the way

*"I'm not insane, my mother had me tested. I'm just building infrastructure."*

## 🤝 Contributing

This is my personal learning journey, but if you:
- Spot errors in my approach
- Have suggestions for improvements  
- Want to share your own experiences
- Found this documentation helpful

Please feel free to open issues or discussions!

## 📄 License

Documentation: MIT License  
3D Models: Creative Commons Attribution-ShareAlike 4.0

---

*"Bazinga! Now let's see if this actually works in practice."*

**Current Version**: v0.1.0 (Just Getting Started)

## 🛠️ Created With

<p align="center">
  <img src="https://img.shields.io/badge/Created%20with-macOS-000000?style=for-the-badge&logo=apple&logoColor=white" alt="Created with macOS"/>
  <img src="https://img.shields.io/badge/Editor-VS%20Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white" alt="VS Code"/>
  <img src="https://img.shields.io/badge/AI%20Assistant-Claude-FF6B35?style=for-the-badge&logo=anthropic&logoColor=white" alt="AI Assistant Claude"/>
  <img src="https://img.shields.io/badge/3D%20Printer-Bambu%20P1S-00A8FF?style=for-the-badge&logo=bambulab&logoColor=white" alt="Bambu P1S"/>
</p>

*In the spirit of the classic "Created with a Mac" - because some things never go out of style.*