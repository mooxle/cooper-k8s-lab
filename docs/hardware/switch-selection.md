# Switch Selection - Network Architecture Analysis

> Pragmatic network design for Cooper'n'80s Kubernetes cluster connectivity.

## 📋 Executive Cooper Summary

> 🤓 *"After analyzing 47 different network topologies, the conclusion is elementary: sometimes less is more."*

**The Decision:**
- **Switch**: D-Link DGS-1100-08V2 - managed L2 with basic L3 features
- **Gateway**: Raspberry Pi 4B for portable scenarios (optional)
- **Architecture**: L2 switching + existing home router for routing
- **Cost**: €40.29 for switch vs €150+ for L3 router solutions
- **Result**: Clean separation of concerns with overlay network handling complexity

**Why This Matters:**
- Home router already provides VLAN segmentation and routing
- Overlay networks (VXLAN/EVPN) handle the actual network intelligence
- L2 switch just needs to move packets efficiently
- Saved €110 can go toward better compute hardware

**Bottom Line:** Why buy a Swiss Army knife when you already own the tools and just need a good blade?

> 🎭 *"Bazinga! The best router is the one you already have in your network rack!"*

---

## 🎯 Requirements Analysis

### Core Requirements
```bash
Physical Connectivity:  8x 1GbE ports minimum
VLAN Support:          Tagged/untagged for home network integration
Management:            SSH/Web for configuration
Automation:            Basic scripting capability
Portability:           Optional gateway for mobile scenarios
```

### Architecture Context
```bash
Home Network:          Native VLAN 192.168.1.0/24 (existing)
                      VLAN 10: 10.0.1.0/24 (K8s Lab)
K8s Networking:        Overlay networks (Calico/Flannel VXLAN)
Proxmox:              VXLAN/EVPN for VM communication
Segmentation:         Handled by existing infrastructure
```

### The Realization
**Key Insight:** "My home router already does VLANs, DHCP, and routing. The K8s lab just needs clean L2 connectivity."

---

## 🔀 Initial Router Approaches (Rejected)

### MikroTik L009UiGS-RM Analysis
```bash
Price:                 ~€150
Features:              Full RouterOS, DHCP, NAT, Container support
Ports:                 8x 1GbE + 1x 2.5G SFP
Verdict:              Overkill - duplicates home router functionality
```

**Why considered:**
- All-in-one solution for routing + switching
- RouterOS learning opportunity
- Portable mode switching capability

**Why rejected:**
- €110 more expensive than necessary
- Redundant with existing home router capabilities
- Complexity without corresponding benefit

### Other Router Considerations
```bash
Ubiquiti UDM-SE:       €500-700 - enterprise overkill
Netgate pfSense:       €150-450 - routing we don't need
DIY pfSense:           €200-300 - time investment not justified
```

---

## 🏗️ Architecture Decision: L2 Switch + Existing Infrastructure

### Network Layer Separation
```
Layer 3 (Routing):      Home Router (existing)
├── Native VLAN:       192.168.1.0/24 (Home Network)
├── VLAN 10:           10.0.1.0/24 (K8s Lab)
├── DHCP Server:       Handles IP assignment for both VLANs
├── NAT/Firewall:      Security and internet access
└── Inter-VLAN:        Controlled routing between networks

Layer 2 (Switching):    D-Link DGS-1100-08V2 (new)
├── Port 1:            Tagged VLAN 10 to home network
├── Ports 2-8:         Untagged for K8s nodes
└── Wire-speed:        Hardware switching between nodes

Overlay (Virtual):      Software-Defined Networks
├── Proxmox VXLAN:     172.16.0.0/16 for VMs
├── K8s CNI:           192.168.0.0/16 for pods
└── EVPN Fabric:       Advanced routing in software
```

### Why This Works
- **Separation of Concerns:** Each layer handles its specific responsibility
- **No Duplication:** Leverages existing home network capabilities
- **Future-Proof:** Overlay networks independent of physical topology
- **Cost-Effective:** Minimal hardware for maximum capability

---

## 📊 Switch Evaluation Matrix

### Candidates Analyzed

| Switch | Price | SSH/CLI | VLAN | Automation | Verdict |
|--------|-------|---------|------|------------|---------|
| **D-Link DGS-1100-08V2** | €40 | ✅ | ✅ | SSH expect | **Selected** ✅ |
| TP-Link TL-SG108E | €30 | ❌ | ✅ | Web scraping | No CLI |
| Netgear GS108E | €35 | ❌ | ✅ | Limited | No SSH |
| Netgear GS108 | €20 | ❌ | ❌ | None | Too basic |
| HP Aruba 1820-8G | €80 | ✅ | ✅ | Ansible native | Over-budget |
| Ubiquiti USW-Lite-8 | €80 | ✅ | ✅ | REST API | Needs controller |

### Decision Factors

**D-Link DGS-1100-08V2 Won Because:**
1. **Ecosystem Consistency:** Same vendor as home network switch
2. **SSH Access:** Enables automation via expect scripts
3. **VLAN Support:** Future flexibility if needed
4. **Price Point:** €40 sweet spot between basic and premium
5. **Management Features:** Web UI + CLI for debugging

---

## 🚀 Implementation Architecture

### Home Network Integration
```bash
Home Router Configuration:
- Native VLAN: 192.168.1.0/24 (existing home network)
- VLAN 10: 10.0.1.0/24 (K8s Lab - already configured)
- DHCP Server: 10.0.1.10-200 (for VLAN 10)
- Gateway: 10.0.1.1 (for VLAN 10)
- Firewall: K8s → Internet ✅, K8s → Home ❌
```

### Switch Configuration
```bash
D-Link Initial Setup:
- Port 1: Tagged VLAN 10 (uplink)
- Ports 2-8: Untagged VLAN 10 (nodes)
- Management IP: 10.0.1.2
- SSH: Enabled for automation
```

### Portable Mode (Optional RPi Gateway)
```bash
When Mobile:
External Network → RPi eth0 (DHCP client)
                ↓ NAT
RPi eth1 → Switch Port 1 → 10.0.1.0/24
```

**RPi Purpose:** Only needed when taking lab to locations without VLAN-capable infrastructure

---

## ❌ Rejected Alternatives

### L3 Router Solutions
**Why Rejected:** Duplicates existing home router functionality
- Pay for features already available
- Adds complexity without benefit
- Budget better spent on compute

### Unmanaged Switches
**Why Rejected:** Lacks future flexibility
- No VLAN support for segmentation
- No management interface for debugging
- Minimal cost savings (€20 vs €40)

### Premium Managed Switches
**Why Rejected:** Features exceed requirements
- Ansible automation nice-to-have, not essential
- PoE unnecessary for mini PCs
- €40-80 premium unjustified

---

## 🎯 Final Configuration

### Selected Components
- **Switch**: D-Link DGS-1100-08V2 - €40.29
- **Cables**: 20x patch cables (10x 0.25m, 10x 0.5m) - €36.80
- **Gateway**: Raspberry Pi 4B (optional, for portability)

### Network Topology
```
Home Network (VLAN 10) → D-Link Switch → K8s Nodes
                                       ↓
                           Overlay Networks (VXLAN/EVPN)
```

### Automation Strategy
```bash
# SSH expect scripts for D-Link automation
./switch-config/
├── backup-config.sh      # Configuration backup
├── vlan-setup.sh        # VLAN configuration
├── port-monitor.sh      # Traffic statistics
└── ansible-wrapper.yml  # Ansible integration via expect
```

---

## 💡 Key Insights

### The Compromise Philosophy
> 🤓 *"Perfect is the enemy of good, and overkill is the enemy of budget."*

**What we learned:**
1. **Existing Infrastructure:** Use what you have before buying new
2. **Layer Separation:** Physical switching ≠ logical routing
3. **Overlay Networks:** Modern networking happens in software
4. **Cost Efficiency:** €40 switch + existing router > €150 all-in-one

### Future Scalability
- **VLANs:** Ready if segmentation needs grow
- **SSH Access:** Automation possible when needed
- **RPi Gateway:** Portability option remains available
- **Overlay Evolution:** VXLAN/EVPN independent of physical layer

---

**Bottom Line:** The D-Link DGS-1100-08V2 provides exactly what's needed - reliable L2 switching with management features - while letting existing infrastructure and overlay networks handle the complexity. Sometimes the smartest architecture is the one that doesn't reinvent the wheel.

> 🎭 *"In the grand experiment of home labs, the control group uses the equipment they already have!"*