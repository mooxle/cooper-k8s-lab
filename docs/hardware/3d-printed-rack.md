# 3D Printed 10-inch Rack

> Building a modular 8U lab rack based on Michael Klements' Lab-Rax design - standing on the shoulders of a true maker giant.

📋 **Technical specifications & component details**: [rack-components.md](rack-components.md)

## 🎯 Goal & Philosophy

### Project Vision
Create a professional 8U (5U base + 3U extension) rack that houses:
- 3x Mini PC nodes (1U each)
- 1x Patch panel with keystone modules (1U)
- 1x L2/L3 switch (1U)
- 1x Power strip (rear 1U)
- Custom Cooper'n'80s branding

*"The need to find the perfect rack solution through modular engineering is not obsessive. It's methodical."*

### Design Philosophy: Building on Giants
Instead of designing from scratch, I'm building on **mklements**' proven Lab-Rax design:

✅ **Extensively tested** - Multiple builds across the maker community  
✅ **Standard hardware** - Metric screws from local hardware store  
✅ **Modular expansion** - Engineered for easy scaling  
✅ **Professional grade** - Clean, rack-mount aesthetic  
✅ **Open source** - Available on GitHub and MakerWorld

**Why mklements' Lab-Rax System?**
- ✅ Brilliant modular engineering approach
- ✅ Uses standard M6 metric screws (hardware store compatible)
- ✅ Proven 10" form factor with professional finish
- ✅ Extensive documentation and community support
- ✅ Open source philosophy with continuous improvements

*"Why reinvent the wheel when you can engineer a better wheel assembly based on proven designs?"*

## 🖨️ Print Strategy & Materials

### Printer Setup: Bambu Lab P1S
**Optimized Settings:**
- **Layer Height**: 0.2mm (balance of speed and quality)
- **Infill**: 15% (optimal strength-to-material ratio)
- **Walls**: 2 perimeters (adequate for structural loads)
- **Support**: Minimal, design-optimized geometries

### Material Strategy

**🖤 PLA Matte (Structural)**
- **Components**: Frame posts, panels, extensions, joiners
- **Color**: Black (professional aesthetic)
- **Rationale**: Easier printing, sufficient strength for static loads
- **Finish**: Matte surface reduces layer lines visibility

**🔧 PETG (Functional)**
- **Components**: Mini PC mounts, switch brackets, heat-sensitive parts
- **Color**: Black (consistency with frame)
- **Rationale**: Superior heat resistance near electronics
- **Properties**: Better impact resistance for removable components

**🧡 PLA Matte Orange (Branding)**
- **Components**: Handles, cable clips, Cooper'n'80s logo
- **Purpose**: Brand identity and functional accents
- **Placement**: Strategic visibility points for brand recognition

### Print Sequence Strategy
1. **Base Frame** → Critical path, start immediately
2. **Test Mounts** → Validate fit with actual hardware
3. **Extensions** → After base frame validation
4. **Custom Elements** → Final phase with branding

## 🎨 Design Considerations

### Modularity Principles
- **Standard Interfaces**: All components use Lab-Rax mounting standards
- **Tool-Free Access**: Front panels removable without disassembly
- **Cable Management**: Integrated routing paths and clip attachment points
- **Future Expansion**: Design supports additional 3U extensions

### Thermal Management
- **Airflow Channels**: Minimum 15mm clearance between components
- **Material Selection**: PETG for heat-sensitive mounting points
- **Ventilation**: Strategic gaps in non-structural areas
- **Heat Isolation**: Thermal barriers between hot components

### Brand Integration
- **Cooper'n'80s Identity**: Orange accent color throughout
- **Logo Placement**: Prominent top-mounted brand element
- **Professional Finish**: Clean lines complement technical aesthetic
- **Functional Branding**: Orange components serve dual purpose

## 📊 Build Progress & Photos

### Phase 1: Base Frame (Current)
**Status**: 🟡 Printing in progress  
**Components**: 5U base posts and panels  
**Material Used**: 281.69g Black PLA Matte  

*Progress photos will be added as build advances*

### Phase 2: Hardware Integration (Pending)
**Next Steps**:
- M6 bolt compatibility testing
- First equipment mounting validation
- Cable routing verification

### Phase 3: Extension & Customization (Future)
**Planned**:
- 3U extension assembly
- Custom orange accent installation
- Brand element integration
- Final system assembly

---

## 🙏 Acknowledgments

**Huge thanks to Michael Klements (mklements)** for creating and open-sourcing the Lab-Rax system. This project wouldn't be possible without his excellent engineering and community contributions.

**Find mklements' work:**
- **GitHub**: [github.com/mklements](https://github.com/mklements)
- **Website**: The Geek Pub
- **YouTube**: Extensive tutorials and build guides
- **MakerWorld**: [Lab-Rax 10" Server Rack](https://makerworld.com/de/models/1464819-lab-rax-10-server-rack-bolted-version-5u)

*"The rack design is theoretically sound thanks to proven engineering. Now let's see if the printer agrees with mklements' theory."*