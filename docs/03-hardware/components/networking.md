# Network Infrastructure

> Connectivity and switching components for Cooper'n'80s lab

## 🎯 Executive Summary

**Decision**: D-Link DGS-1100-08V2 managed L2 switch + existing home router VLAN  
**Strategy**: L2 switching + existing infrastructure for routing  
**Cost**: €40 vs €150+ for L3 solutions  
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

**Design**: Custom 1U rack-mount panel  
**Capacity**: 10x keystone modules  
**Material**: Black PLA Matte  
**Source**: [MakerWorld Community Design](https://makerworld.com/de/models/907994-patchpanel-for-10-keystones-10-rack)

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
│  1   │ Uplink          │ -            │ Untagged VLAN 10 │
│  2   │ Mini PC Node 1  │ 10.0.1.10    │ Untagged VLAN 10 │
│  3   │ Mini PC Node 2  │ 10.0.1.11    │ Untagged VLAN 10 │
│  4   │ Mini PC Node 3  │ 10.0.1.12    │ Untagged VLAN 10 │
│  5   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  6   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  7   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  8   │ Admin/Laptop    │ 10.0.1.100   │ Untagged VLAN 10 │
└──────┴─────────────────┴──────────────┴──────────────────┘
```

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

## 💰 Cost Analysis

### Network Components
```
D-Link Switch:        €40.29
Keystone Modules:     €33.99  
Patch Cables:         €36.80
3D Printed Panel:     ~€3.00 (material)
Total Network:        €114.08
```

### Alternative Comparison
```
L3 Router Solution:   €150-300
Premium Managed:      €80-150  
Unmanaged Switch:     €20-30
Selected Solution:    €40
```

**Savings**: €110+ reinvested in compute hardware

## 🔧 Physical Integration

### Rack Mounting
- **Switch Position**: 7U (near top for cable management)
- **Patch Panel**: 8U (top position for easy access)  
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

**Philosophy**: *"Perfect is the enemy of good, and overkill is the enemy of budget. Sometimes the smartest architecture uses what you already have."*