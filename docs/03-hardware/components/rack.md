# Rack System & Enclosure

> Custom 3D-printed 8U rack based on mklements' Lab-Rax design

## 🎯 Overview

**Design**: mklements Lab-Rax 8U (5U base + 3U extension)  
**Form Factor**: 10-inch rack standard  
**Material**: PLA Matte (structure) + PETG (heat-sensitive components)  
**Branding**: Custom Cooper'n'80s elements in orange accents

> *"Standing on the shoulders of a true maker giant"* - Building on mklements' proven engineering

## 🏗️ Design Philosophy

### Why mklements' Lab-Rax System
✅ **Extensively tested** - Multiple builds across maker community  
✅ **Standard hardware** - M6 metric screws from local hardware store  
✅ **Modular expansion** - Engineered for easy scaling  
✅ **Professional grade** - Clean, rack-mount aesthetic  
✅ **Open source** - Available on GitHub and MakerWorld

### Design Principles
- **Modularity**: Standard interfaces between all components
- **Tool-Free Access**: Front panels removable without disassembly  
- **Cable Management**: Integrated routing paths and clip attachment
- **Future Expansion**: Supports additional 3U extensions
- **Professional Finish**: Enterprise aesthetics at lab scale

## 📐 Rack Configuration (8U Total)

### Equipment Layout (UPDATED)

```
╔══════════════════════════════╗
║ 8U │ 🔌 Patch Panel (0.5U)   ║ ← 12-port keystone panel
║    │ 📦 Cover/Free (0.5U)    ║ ← Future expansion or cover
╠══════════════════════════════╣
║ 7U │ 🌐 D-Link DGS-1100      ║ ← 8-port managed switch
╠══════════════════════════════╣
║ 6U │ 📦 Cover/Free          ║ ← Future expansion zone
╠══════════════════════════════╣
║ 5U │ 🖥️ Mini PC Node #3      ║ ← Dell OptiPlex 3080 Micro
╠══════════════════════════════╣
║ 4U │ 🖥️ Mini PC Node #2      ║ ← Dell OptiPlex 3080 Micro  
╠══════════════════════════════╣
║ 3U │ 🖥️ Mini PC Node #1      ║ ← Dell OptiPlex 3080 Micro
╠══════════════════════════════╣
║ 2U │ 📦 Cover/Free          ║ ← Cable management zone
╠══════════════════════════════╣
║ 1U │ ⚡ Mini PC Power Tray   ║ ← Dedicated power supply organization
╚══════════════════════════════╝
```

### Zone Organization (UPDATED)
- **Network Infrastructure** (7.5U-8U): Switch and patch panel for connectivity
- **Computing Cluster** (3U-5U): Three Mini PCs forming Kubernetes cluster
- **Expansion Zone** (6U): Reserved for future equipment (NAS, monitoring, etc.)
- **Cable Management** (2U): Hidden area for cable routing and organization  
- **Power Management** (1U): Dedicated tray for Mini PC power adapters
- **Rear Infrastructure**: PDU power distribution and uplink connections

## 🖨️ Print Specifications

### Base Components (mklements design)

| Component | Quantity | Material | Color | Status |
|-----------|----------|----------|-------|--------|
| Base Posts | 4 | PLA Matte | Black | ✅ Completed |
| Extension Posts | 4 | PLA Matte | Black | ✅ Completed |
| Side Joiners | 4 | PLA Matte | Black | ✅ Completed |
| Extension Connectors | 4 | PLA Matte | Black | ✅ Completed |
| Horizontal Joiners - Solid | 2 | PLA Matte | Black | ✅ Completed |
| Horizontal Joiners - Grid | 2 | PLA Matte | Orange | ✅ Completed |
| Handles | 2 | PLA Matte | Orange | ✅ Completed |
| Feet | 2 | PLA Matte | Orange | ⚪ Pending hardware |

### Custom Components (UPDATED)

| Component | Quantity | Material | Color | Purpose |
|-----------|----------|----------|-------|---------|
| Cooper'n'80s Logo | 1 | PLA Matte | Orange/Black | Top rail branding |
| Cooper'n'80s Strips | 2 | PLA Matte | Orange/Black | Side panel branding |
| Custom Side Panels (5U) | 2 | PLA Matte | Black/Orange | Ventilation + branding |
| Custom Side Panels (1.5U) | 4 | PLA Matte | Black/Orange | Ventilation + branding |
| Mini PC Mounts | 3 | PETG | Black | Heat-resistant equipment mounting |
| Power Supply Tray | 1 | PETG | Black | 1U power adapter organization |
| Switch Mount | 1 | PETG | Black | D-Link DGS-1100-08V2 bracket |
| Front Covers (1U) | 2 | PLA Matte | Black | 2U and 6U aesthetic panels |

## 🎨 Custom Design Elements

### Custom Side Panel Ventilation

> *"As Sheldon Cooper would say: 'It's not just ventilation... it's an elegant equation of airflow, symmetry, and personal branding.'"*

**Design Foundation**: Building upon mklements' proven hexagon ventilation patterns

#### Source Models & Attribution

| Original Model | Author | Purpose | Link |
|----------------|--------|---------|------|
| **5U Sidepanels with Hex Pattern Ventilation** | AlanMG_3D | Ventilation inspiration | [MakerWorld](https://makerworld.com/de/models/1577920-5u-sidepanels-with-hex-pattern-ventilation?from=email_notification#profileId-1703572) |
| **5U Hexpattern Extension Panel** | AlanMG_3D | Base geometry for modifications | [MakerWorld](https://makerworld.com/de/models/1618034-5u-hexpattern-extention-panel#profileId-1708066) |

#### Cooper'n'80s Modifications

**Design Process**:
1. **Base Model**: Used AlanMG_3D's 5U hexpattern extension panel as foundation
2. **Logo Integration**: Embedded custom Cooper'n'80s logo using Tinkercad
3. **Height Adaptation**: Created additional 1.5U elements for 8U total rack height
4. **Enhanced Ventilation**: Added extra ventilation slots in 1.5U panels
5. **Color Customization**: Applied dual-color scheme in Bambu Lab Studio

**Visual Design Preview**:

| 5U Sidepanel with Logo | 1.5U Sidepanel | Logo Strips for Case |
|:----------------------:|:--------------:|:-------------------:|
| ![5U Sidepanel Logo](../../../assets/5U_Sidepanel_Logo.png) | ![1.5U Sidepanel](../../../assets/1_5U_Sidepanel.png) | ![Logo Strips](../../../assets/Logos_long_case.png) |
| Main side panel with integrated Cooper'n'80s logo | Additional ventilation panels for 8U height | Long-format strips for exterior branding |

**Specific Changes**:
- **5U Panel**: Original hex pattern + integrated Cooper'n'80s logo cutout
- **1.5U Panels**: New design with additional ventilation for optimal airflow
- **Logo Strips**: Extended branding elements for case exterior mounting
- **Material Strategy**: Black base structure + orange accent elements
- **Functional Enhancement**: Improved thermal management through expanded ventilation area

#### Design Tools & Workflow
- **Modification Platform**: Tinkercad for logo integration and panel adaptation
- **Print Preparation**: Bambu Lab Studio for dual-color configuration
- **Quality Approach**: Maintained mklements' proven ventilation geometry while adding custom elements

#### Final Assets
- [`5U_Sidepanel_Logo.stl`](../../../assets/5U_Sidepanel_Logo.stl) - Modified with logo integration
- [`1_5U_Sidepanel.stl`](../../../assets/1_5U_Sidepanel.stl) - Custom height extension panels

**Philosophy**: Standing on the shoulders of giants (mklements) while adding precisely calculated personal touches for optimal form and function.

### Branding Elements

**Cooper'n'80s Logo**:
- Single color: Black PLA (professional, subtle)
- Dual color: Black base + Orange accent (full brand impact)
- Mounting: Top ventilation rail for prominent visibility

**Available Assets**:
- [`Cooper'n80s.stl`](../../../assets/Cooper'n80s.stl) - Single color
- [`Cooper'n80s.3mf`](../../../assets/Cooper'n80s.3mf) - Dual color  
- [`Cooper'n80s_long.3mf`](../../../assets/Cooper'n%2780s_long.3mf) - Exterior strips

## 🔧 Assembly Hardware

### Mounting Hardware
- **Bolts**: M6×12mm flanged screws (black zinc plated)
- **Nuts**: M6 hex nuts (stainless steel DIN 934)
- **Quantity**: 100 screws, 50 nuts
- **Source**: eBay (khs_2005) - €31.49

### Material Strategy
- **PLA Matte (Structural)**: Frame, panels, non-heat components
- **PETG (Functional)**: Mini PC mounts, switch brackets near electronics
- **Color Coordination**: Black primary + Orange accents for brand consistency

## 🌡️ Thermal Management

### Airflow Design (UPDATED)
- **Ventilation**: Hex patterns in side panels for passive airflow
- **Clearance**: Minimum 15mm between components
- **Material Selection**: PETG for heat-sensitive mounting points
- **Heat Isolation**: Thermal barriers between hot components

### Component Spacing (UPDATED)
```
Patch Panel (8U) ←→ 22mm spacing ←→ Switch (7U)
Switch (7U) ←→ 44mm spacing ←→ Node 3 (5U) 
Node 3 (5U) ←→ 44mm spacing ←→ Node 2 (4U)
Node 2 (4U) ←→ 44mm spacing ←→ Node 1 (3U)
Node 1 (3U) ←→ 44mm spacing ←→ Cable Mgmt (2U)
Cable Mgmt (2U) ←→ 44mm spacing ←→ Power Tray (1U)
```

**Thermal Strategy**:
- **Heat Sources**: Mini PCs (3U-5U) generate most heat
- **Airflow Pattern**: Bottom-to-top natural convection
- **Cool Zone**: Power tray (1U) and cable management (2U) at bottom
- **Hot Zone**: Computing nodes (3U-5U) in middle with maximum ventilation
- **Infrastructure**: Network equipment (7U-8U) at top, minimal heat generation

## 📊 Build Progress

### Current Status (Material Used)
```
Frame Base:           722.26g Black PLA (€10.11)
Extensions:           ~200g Black PLA (estimated)
Custom Elements:      ~150g Orange PLA (estimated)
Functional Mounts:    ~100g Black PETG (estimated)
Total Estimate:       ~1172g mixed materials (~€16.50)
```

### Assembly Progress

![Frame Components Complete](../assembly/photos/frame-parts_800.png)
*All printed frame components ready for assembly - Base posts, extension posts, side joiners, extension connectors, and Cooper'n'80s branding elements*

### Assembly Phases
1. **✅ Base Frame** - All structural components assembled
2. **✅ Hardware Assembly** - Frame construction completed with M6 bolts
3. **✅ Custom Elements** - Side panels and branding completed
4. **⚪ Equipment Integration** - Component mounting and cable management pending

## 🔧 Equipment Mounting (UPDATED)

### Mini PC Integration
- **Node Positions**: 3U, 4U, 5U (Dell OptiPlex 3080 Micro)
- **Mount Type**: Custom PETG brackets (heat resistance)
- **Ventilation**: Open-frame design for airflow
- **Access**: Front-loading with rear cable management
- **Power**: Individual adapters in dedicated 1U power tray

### Network Equipment
- **Switch Position**: 7U (D-Link DGS-1100-08V2)
- **Patch Panel**: 8U (0.5U keystone panel)
- **Cable Management**: Integrated clips and routing channels
- **Service Access**: Tool-free panel removal

### Power Distribution (NEW)
**1U Power Tray Configuration**:
```
┌─────────────────────────────────────────────────┐
│  [PSU 1]     [PSU 2]     [PSU 3]     [Cable]   │ 1U Tray
│   Node 1     Node 2     Node 3      Mgmt       │
└─────────────────────────────────────────────────┘
       │          │          │          │
       ▼          ▼          ▼          ▼
   [IEC Cable][IEC Cable][IEC Cable][Power Strip]
       │          │          │          │
       └──────────┴──────────┴──────────┘
                      │
              Rear-mounted PDU
```

**Power Management**:
- **Individual Power**: Each Mini PC has dedicated power adapter
- **Tray Organization**: 3D-printed organizer for clean power management
- **Cable Routing**: IEC cables from tray to rear PDU
- **Isolation**: No shared power between nodes for reliability

## 🎯 Future Expansion

### Modular Growth
- **3U Extensions**: Additional rack height as needed
- **Component Flexibility**: Standard mounting for various equipment
- **Cable Infrastructure**: Expandable routing system
- **Power Scaling**: Additional PDUs as required

### Planned Additions
- **NAS/Storage**: Potential 2U unit at 6U position
- **Network Upgrades**: Additional switches or routers
- **Monitoring**: Dedicated monitoring displays or hardware
- **Additional Compute**: Extra nodes for cluster expansion

## 📚 Related Documentation

- **[3D Printing Process](../assembly/3d-printing.md)** - Detailed build procedures
- **[Components Overview](../README.md)** - Complete hardware list
- **[Assembly Progress](../assembly/)** - Build status and photos
- **[Network Integration](networking.md)** - Connectivity and switch mounting

## 🙏 Acknowledgments

**Huge thanks to Michael Klements (mklements)** for creating and open-sourcing the Lab-Rax system.

**Find mklements' work**:
- **GitHub**: [github.com/mklements](https://github.com/mklements)
- **MakerWorld**: [Lab-Rax 10" Server Rack](https://makerworld.com/de/models/1464819-lab-rax-10-server-rack-bolted-version-5u)
- **Website**: The Geek Pub
- **YouTube**: Extensive tutorials and build guides

---

**Philosophy**: *"The rack design is theoretically sound thanks to proven engineering. Now let's see if the printer agrees with mklements' theory - and how our power management improvements enhance the original design."*