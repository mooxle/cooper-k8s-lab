# 3D Printed 10-inch Rack

> Building a modular 8U lab rack based on Michael Klements' Lab-Rax design - standing on the shoulders of a true maker giant.

## 🎯 Goal

Create a professional 8U (5U base + 3U extension) rack that houses:
- 3x Mini PC nodes (1U each)
- 1x Patch panel with keystone modules (1U)
- 1x L2/L3 switch (1U)
- 1x Power strip (rear 1U)
- Custom Cooper'n'80s branding

*"The need to find the perfect rack solution through modular engineering is not obsessive. It's methodical."*

## 📐 Design Philosophy

### Why Michael Klements' Lab-Rax System?
Instead of designing from scratch, I'm building on **mklements**' proven Lab-Rax design:

✅ **Extensively tested** - Multiple builds across the maker community  
✅ **Standard hardware** - Metric screws from local hardware store  
✅ **Modular expansion** - Engineered for easy scaling  
✅ **Professional grade** - Clean, rack-mount aesthetic  
✅ **Open source** - Available on GitHub and MakerWorld

**Credit where credit is due**: This entire approach is based on Michael Klements' excellent Lab-Rax system.

*"Why reinvent the wheel when you can engineer a better wheel assembly based on proven designs?"*

## 🧩 Component Selection

### Base Design: Michael Klements' Lab-Rax 10"
**Designer**: Michael Klements (**mklements**)  
**GitHub**: [github.com/mklements](https://github.com/mklements)  
**MakerWorld**: [Lab-Rax 10" Server Rack (Bolted Version)](https://makerworld.com/de/models/1464819-lab-rax-10-server-rack-bolted-version-5u?from=search#profileId-1527877)

**Why mklements' design:**
- ✅ Brilliant modular engineering approach
- ✅ Uses standard metric screws (M3, M4 from hardware store)
- ✅ Proven 10" form factor with professional finish
- ✅ Extensive documentation and community support
- ✅ Open source philosophy with continuous improvements

### Extension Components
**3U Extension**: Lab-Rax 1.5U Posts & Panels (2x for 3U total)  
**Connection System**: Lab-Rax Extension Side Panels & Post Joiners

**All credit to mklements for the modular system that makes this expansion possible!**

## 🏗️ Rack Layout (8U Total)

### Front & Rear Configuration

| U# | Front Equipment | Rear Equipment | Notes |
|----|----------------|----------------|-------|
| **8U** | 🔌 **Patch Panel** (Keystone) | *Free* | Network termination |
| **7U** | 🌐 **L2/L3 Switch** | *Free* | Core networking |
| **6U** | *Free* | *Free* | Future expansion |
| **5U** | *Free* | *Free* | Future expansion |
| **4U** | *Free* | *Free* | Future expansion |
| **3U** | 🖥️ **Mini PC Node #3** | *Free* | K8s worker/control |
| **2U** | 🖥️ **Mini PC Node #2** | *Free* | K8s worker/control |
| **1U** | 🖥️ **Mini PC Node #1** | ⚡ **Power Strip** | Base node + power |

### Visual Layout
```
Front View                    Rear View
┌─────────────────────────┐   ┌─────────────────────────┐
│ 8U │ 🔌 Patch Panel     │   │ 8U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 7U │ 🌐 L2/L3 Switch    │   │ 7U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 6U │      Free          │   │ 6U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 5U │      Free          │   │ 5U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 4U │      Free          │   │ 4U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 3U │ 🖥️ Mini PC #3      │   │ 3U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 2U │ 🖥️ Mini PC #2      │   │ 2U │      Free          │
├────┼────────────────────┤   ├────┼────────────────────┤
│ 1U │ 🖥️ Mini PC #1      │   │ 1U │ ⚡ Power Strip     │
└────┴────────────────────┘   └────┴────────────────────┘
```

### Equipment Zones
- **🖥️ Compute Zone** (1U-3U): Three mini PCs for Kubernetes cluster
- **🌐 Network Zone** (7U-8U): Switch and patch panel for connectivity  
- **⚡ Power Zone** (Rear 1U): Centralized power distribution
- **📈 Expansion Zone** (4U-6U): Reserved for future growth

## 🖨️ Print Strategy

### Printer: Bambu Lab P1S
**Settings Overview:**
- **Infill**: 15% (good strength-to-material ratio)
- **Walls**: 2 perimeters (adequate for structural parts)
- **Support**: As needed for overhangs

### Material Strategy
**Frame Components** → **PLA Matte**
- Base rack posts and panels
- Extension components
- Joiners and structural elements
- **Rationale**: Easier printing, sufficient strength for frame

**Equipment Inserts** → **PETG**
- Node mounting trays
- Switch brackets
- Heat-sensitive components
- **Rationale**: Better heat resistance near warm electronics

### Print Queue (Planned)

*Current focus: Base rack frame components with PLA Matte*

Detailed print scheduling will be documented in the build log as components are completed.

## 🔧 Next Steps

### 1. Hardware-Specific Insert Design
Based on final component selection:
- Design exact mounting for chosen mini PCs
- Create switch-specific mounting bracket
- Plan keystone patch panel layout

### 2. Custom Elements
- **Cooper'n'80s 3D logo** for top mounting
- Side panel design (ventilation? branding?)
- Top cover options

### 3. Hardware Procurement
- Metric screws (M3, M4) from local hardware store
- Heat-set inserts (if needed for PETG components)
- Rack nuts for equipment mounting

## 📊 Build Progress

### Current Status: Planning & Sourcing
- [x] Base design selected and analyzed
- [x] Extension strategy planned
- [x] Print material strategy defined
- [ ] Source files downloaded
- [ ] First prints started

### Build Log
*Progress photos and observations will be added as printing progresses...*

**Today**: [Date]
- Starting with base rack components
- Testing PLA Matte settings on Bambu P1S

## 🎨 Design Considerations

### Still To Decide:
- **Side panels**: Solid, ventilated, or hybrid design?
- **Top cover**: Access panel, ventilation, or fully enclosed?
- **Branding placement**: Just top logo or additional elements?
- **Color scheme**: All matte black or accent colors?

### Future Expansion Options:
- Additional 1.5U sections can be added
- Different insert designs for changing hardware
- Modular approach allows easy modifications

---

**Status**: 🚧 Design Phase  
**Next Update**: After first print components complete

## 🙏 Acknowledgments

**Huge thanks to Michael Klements (mklements)** for creating and open-sourcing the Lab-Rax system. This project wouldn't be possible without his excellent engineering and community contributions.

Check out his work:
- **GitHub**: [github.com/mklements](https://github.com/mklements)
- **Website**: The Geek Pub
- **YouTube**: Extensive tutorials and build guides

*"The rack design is theoretically sound thanks to proven engineering. Now let's see if the printer agrees with mklements' theory."*