# Network Infrastructure

> Connectivity and switching components for Cooper'n'80s lab

## 🎯 Executive Summary

**Decision**: D-Link DGS-1100-08V2 managed L2 switch + existing home router VLAN  
**Strategy**: L2 switching + existing infrastructure for routing  
**Result**: Clean separation of concerns with overlay networks handling complexity

> *"Why buy a Swiss Army knife when you already own the tools and just need a good blade?"*

## 🌐 Architecture Philosophy

### Layer Separation Strategy
```
Layer 3 (Routing):     Home Router (existing)
├── VLAN 10:          10.0.1.0/24 (K8s Lab)
├── DHCP/DNS:         Pi-hole integration
└── Internet Gateway: NAT and firewall

Layer 2 (Switching):   D-Link DGS-1100-08V2 (new)
├── Port 1:           Tagged VLAN 10 uplink
├── Ports 2-8:        Untagged for lab nodes
└── Management:       SSH + Web interface

Overlay (Virtual):     Software-Defined Networks
├── K8s CNI:          Calico/Flannel VXLAN
├── Proxmox VXLAN:    VM-to-VM communication
└── Service Mesh:     Application networking
```

**Rationale**: Home router already provides VLAN segmentation and routing. Lab just needs efficient L2 connectivity.

## 🔧 Core Components

### Primary Switch: D-Link DGS-1100-08V2

**Specifications**:
- **Ports**: 8x Gigabit Ethernet
- **Management**: SSH + Web interface  
- **VLAN Support**: 802.1Q tagged/untagged
- **Automation**: SSH via expect scripts
- **Price**: €40.29

**Why Selected**:
✅ **Ecosystem Consistency** - Same vendor as home network  
✅ **SSH Access** - Enables automation  
✅ **VLAN Support** - Future flexibility  
✅ **Price Point** - Sweet spot between basic and premium  
✅ **Management Features** - Web UI + CLI for debugging

### Patch Panel: 3D-Printed Keystone

**Design**: Custom 0.5U rack-mount panel  
**Capacity**: 12x keystone modules  
**Material**: Black PLA Matte  
**Source**: [mklements 1/2U 12-Port Keystone Panel](https://makerworld.com/de/models/1293816-1-2u-12-port-keystone-panel-for-10-rack#profileId-1324489)
**Advantage**: 0.5U height provides optimal equipment spacing and improved thermal management

**Port Layout Configuration:**

**Patch Panel Visual:**
```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬────┬────┬────┐
│ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │ 7 │ 8 │ 9 │ 10 │ 11 │ 12 │
├───┼───┼───┼───┼───┼───┼───┼───┼───┼────┼────┼────┤
│🔌 │🔌 │🔌 │🔌 │🔌 │🔌 │🔌 │🟠 │🟠 │ 🟠 │ 🟠 │🔌 │
└───┴───┴───┴───┴───┴───┴───┴───┴───┴────┴────┴────┘
```

**Port Assignments:**
- **Ports 1-3**: Mini PC Nodes (10.0.1.10-12)
- **Ports 4-7**: **Reserved** for future expansion (silver keystones)
- **Ports 8-11**: **🟠 Orange blank covers** (visual expansion indicator)
- **Port 12**: **Uplink** to home network (DGS-1210 Port 18)

**Keystone Blank Covers**: [klayf Keystone Blank Insert](https://makerworld.com/de/models/1265159-keystone-blank-insert-cover-for-petg-pla?from=search#profileId-1293411)
- **Material**: Orange PLA for brand consistency
- **Purpose**: Cover unused ports 8-11, visual expansion indicator
- **Design**: Professional blank covers, 24min print time

### Keystone Modules: deleyCON Cat7 Metal

**Specifications**:
- **Type**: Female-to-female RJ45 couplers
- **Rating**: Cat7, 600 MHz, 10 Gbps
- **Material**: Metal housing (silver finish)
- **Quantity**: 12x (10 active + 2 spare)
- **Price**: €33.99

**Trade-off Note**: Only available in silver (Cat6a would offer black options)

### Patch Cables

**Configuration**:
- **Short runs**: 10x 0.25m orange cables (€17.90)
- **Medium runs**: 10x 0.5m orange cables (€18.90)  
- **Total**: 20 cables for comprehensive connectivity
- **Color**: Orange (brand consistency + high visibility)

## 📐 Port Assignment

### Switch Port Layout

```
┌──────┬─────────────────┬──────────────┬──────────────────┐
│ Port │ Device          │ IP Address   │ Configuration    │
├──────┼─────────────────┼──────────────┼──────────────────┤
│  1   │ Mini PC Node 1  │ 10.0.1.10    │ Untagged VLAN 10 │
│  2   │ Mini PC Node 2  │ 10.0.1.11    │ Untagged VLAN 10 │
│  3   │ Mini PC Node 3  │ 10.0.1.12    │ Untagged VLAN 10 │
│  4   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  5   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  6   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  7   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  8   │ Uplink          │ -            │ Untagged VLAN 10 │
└──────┴─────────────────┴──────────────┴──────────────────┘
```

### Patch Panel to Switch Mapping

| Patch Panel Port | Switch Port | Device | IP Address | Cable | Status |
|:----------------:|:-----------:|--------|------------|-------|--------|
| 1 | 1 | Mini PC Node 1 | 10.0.1.10 | 0.25m Orange | Planned |
| 2 | 2 | Mini PC Node 2 | 10.0.1.11 | 0.5m Orange | Planned |
| 3 | 3 | Mini PC Node 3 | 10.0.1.12 | 0.5m Orange | Planned |
| 4-7 | - | **Reserved** | - | - | Future expansion |
| 8-11 | - | **🟠 Blank Covers** | - | - | Visual indicators |
| 12 | 8 | **Uplink** | - | 1-2m to DGS-1210 | Active |

**Direct Switch Connections:**
| Switch Port | Device | Cable | Notes |
|:-----------:|--------|-------|-------|
| 2 | Admin/Laptop | 0.5m Orange | Direct connection |
| 3 | Reserved | - | Available for expansion |
| 7-8 | Reserved | - | Available for expansion |

**Visual Separation Strategy**:
- **Active Equipment**: Ports 1-3 + 12 (silver keystones)
- **Reserved Expansion**: Ports 4-7 (silver keystones, ready for use)
- **Visual Indicators**: Ports 8-11 (orange blank covers)
- **Uplink Separation**: Port 12 dedicated for home network connection

### Network Addressing

**Lab Network**: 10.0.1.0/24 (VLAN 10)  
**Gateway**: 10.0.1.1 (home router VLAN interface)  
**DNS**: Pi-hole (192.168.1.99) via router  

**Static Assignments** (DHCP reservations):
- 10.0.1.2: Switch management
- 10.0.1.10-12: Mini PC nodes  
- 10.0.1.100: Admin laptop
- 10.0.1.101-200: DHCP pool for temporary devices

## 🔄 Alternative Configurations

### Portable Mode (Future)

**Raspberry Pi Gateway** for mobile scenarios:
```
External Network → RPi eth0 (DHCP client)
                ↓ NAT
RPi eth1 → Switch Port 1 → 10.0.1.0/24 (internal)
```

**Use Cases**:
- Taking lab to conferences/training
- Locations without VLAN-capable infrastructure
- Isolated testing environments

**Components** (optional):
- Raspberry Pi 4B with USB Ethernet adapter
- Custom NAT/DHCP configuration
- VPN server for remote access

## 🛠️ Configuration Management

### Initial Switch Setup
```bash
# Web interface access
http://10.0.1.2

# VLAN Configuration
- All ports: Untagged members of VLAN 10
- PVID: 10 for all ports
- Management VLAN: 10
- No tagged VLANs (simplified L2 operation)
```

### Automation Framework
```bash
# SSH expect scripts for D-Link automation
./switch-config/
├── backup-config.sh      # Configuration backup
├── vlan-setup.sh        # VLAN configuration  
├── port-monitor.sh      # Traffic statistics
└── ansible-wrapper.yml  # Ansible integration
```

## 📊 Performance Expectations

### Throughput
- **Switch Capacity**: Wire-speed forwarding (8 Gbps aggregate)
- **Node-to-Node**: Full gigabit between any two ports
- **Uplink**: Gigabit to home network (internet bottleneck)

### Latency
- **L2 Switching**: <1ms between lab nodes
- **Internet Access**: Home network routing + ISP latency
- **Overlay Networks**: +2-5ms for VXLAN encapsulation

## 💰 Component Summary

### Network Components
```
D-Link Switch:        €40.29
Keystone Modules:     €33.99  
Patch Cables:         €36.80
3D Printed Panel:     ~€3.00 (material)
Total Network:        €114.08
```

## 🔧 Physical Integration

### Rack Mounting
- **Switch Position**: 7.5U (optimal spacing with 0.5U patch panel)
- **Patch Panel**: 8U (0.5U form factor for space efficiency)  
- **Cable Runs**: 0.25-0.5m lengths minimize cable bulk
- **Management**: Rear access for power and uplink

### Cable Management
- **Routing**: Through custom 3D-printed cable clips
- **Organization**: Orange cables for lab, other colors for management
- **Flexibility**: Tool-free reconfiguration for experiments

## 📚 Related Documentation

- **[Network Topology](../../02-design/network-topology.md)** - Complete network architecture
- **[Rack Design](rack.md)** - Physical mounting specifications  
- **[Switch Selection](../../hardware/switch-selection.md)** - Original decision analysis

---

**Philosophy**: *"Perfect is the enemy of good. Sometimes the smartest architecture uses what you already have."*