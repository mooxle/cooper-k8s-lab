# Hardware Components & Assembly

> Physical infrastructure for the Cooper'n'80s Kubernetes lab

## 📁 This Section

| Category | Documents | Status |
|----------|-----------|--------|
| **[Components](components/)** | Individual hardware specifications | ✅ Defined |
| **[Assembly](assembly/)** | Build process and procedures | 🟡 In Progress |
| **[Shopping](shopping-list.md)** | Purchase tracking and costs | ✅ Active |

## 🎯 Hardware Overview

**Target Configuration**: 3-node Kubernetes cluster in custom 8U rack
- **Compute**: 3x Mini PC (Intel i5-10500T, 16-32GB RAM, 512GB NVMe)
- **Networking**: Managed L2 switch with VLAN support
- **Storage**: Local NVMe + future NAS integration
- **Rack**: Custom 3D-printed 8U enclosure

## 🔧 Component Categories

### Computing Nodes
**[Mini PCs](components/mini-pcs.md)** - Primary compute platform
- CPU: Intel i5-10500T (6C/12T) for Hyperthreading advantage
- Memory: 16GB (upgradeable to 32GB)
- Storage: 512GB NVMe SSD minimum
- Platform: Dell OptiPlex preferred, alternatives viable

### Network Infrastructure  
**[Networking](components/networking.md)** - Connectivity and switching
- Switch: D-Link DGS-1100-08V2 (managed L2)
- Patch Panel: 3D-printed with keystone modules
- Cables: 20x patch cables (various lengths)

### Physical Infrastructure
**[Rack System](components/rack.md)** - Enclosure and mounting
- Design: mklements Lab-Rax 8U (5U base + 3U extension)
- Material: PLA Matte (structure) + PETG (heat-sensitive)
- Branding: Custom Cooper'n'80s elements in orange

### Power & Accessories
**[Power Distribution](components/power.md)** - Electrical infrastructure
- PDU: DIGITUS DN-95418 (1U rack-mount, 4-outlet)
- Cables: IEC and regional power connections

## 🖨️ Assembly Progress

### Current Status
```
🏗️ Rack Frame      ████████████ ✅ Assembly complete, professional finish
🔧 Components      ████████░░░░ 🟡 Network equipment delivered, mounting pending
📐 Assembly        ████████░░░░ 🟡 Equipment integration phase
🔌 Integration     ████░░░░░░░░ ⚪ Final system assembly pending
```

### Build Phases
1. **✅ [3D Printing](assembly/3d-printing.md)** - Complete rack fabrication with custom branding
2. **✅ [Frame Assembly](assembly/photos/)** - Professional 8U structure assembled
3. **🟡 Equipment Mounting** - Switch brackets, patch panel, power distribution
4. **⚪ System Integration** - Mini PC installation and connectivity testing

## 💰 Budget Overview

**Total Spent**: €172.67  
**Committed**: €300-400 (Mini PCs pending)  
**Target Budget**: <€600 total project cost

**[Detailed Shopping List](shopping-list.md)** - Complete purchase tracking

## 🎯 Design Philosophy

**Enterprise Standards at Lab Scale**:
- Professional aesthetics and clean cable management
- Modular design enabling easy expansion
- Tool-free access for maintenance
- Standard mounting and connection interfaces

**Cooper'n'80s Branding**:
- Consistent orange accent color throughout
- Custom logos and functional branding elements
- Professional finish complementing technical capability

## 📚 Navigation

**Current Focus**: [3D Printing Progress](assembly/3d-printing.md)  
**Next Steps**: Mini PC sourcing and component integration  
**Related**: [Network Topology](../02-design/network-topology.md) for logical connectivity

---

**Philosophy**: *"The rack design is theoretically sound thanks to proven engineering. Now let's see if the printer agrees with mklements' theory."*