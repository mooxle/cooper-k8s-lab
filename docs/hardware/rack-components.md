# Rack Components - Technical Specifications

> Detailed component list and specifications for the Cooper'n'80s 8U rack build.

---

## Table of Contents
- [Rack Components - Technical Specifications](#rack-components---technical-specifications)
  - [Table of Contents](#table-of-contents)
  - [📊 Component Overview](#-component-overview)
  - [🏗️ Rack Layout (8U Total)](#️-rack-layout-8u-total)
    - [Equipment Distribution](#equipment-distribution)
    - [Zones](#zones)
  - [🖨️ 3D Printed Components](#️-3d-printed-components)
    - [Base Rack System](#base-rack-system)
    - [Custom Components](#custom-components)
    - [Custom Side Panels](#custom-side-panels)
      - [Inspiration \& Base Work](#inspiration--base-work)
      - [Modifications](#modifications)
      - [Files](#files)
      - [Preview](#preview)
      - [Fun Fact](#fun-fact)
    - [Logos](#logos)
      - [Preview](#preview-1)
    - [Patch Panel](#patch-panel)
  - [🔧 Hardware Components](#-hardware-components)
    - [Networking](#networking)
    - [Power Distribution](#power-distribution)
    - [Mounting Hardware](#mounting-hardware)
  - [📐 Assembly Specifications](#-assembly-specifications)
    - [Mounting Standards](#mounting-standards)
    - [Cable Management](#cable-management)
    - [Material Specifications](#material-specifications)

---

## 📊 Component Overview

| Category | Component | Source | Status | Notes |
|----------|-----------|--------|--------|-------|
| **Base Frame** | [Lab-Rax 10" (5U)](#base-rack-system) | mklements/MakerWorld | 🟡 Printing | Black PLA Matte |
| **Extension** | [Lab-Rax 3U Extension](#base-rack-system) | mklements/MakerWorld | ⚪ Pending | Black PLA Matte |
| **Patch Panel** | [10-Keystone Panel](#patch-panel) | MakerWorld | ⚪ Pending | Black PLA Matte |
| **Keystone Jacks** | [deleyCON Cat7 Metal](#networking) | deleyCON | ✅ Ordered | 10 Gbps capability |
| **Power Strip** | [DIGITUS DN-95418](#power-distribution) | DIGITUS | ✅ Ordered | 4-outlet, 1HE |
| **Hardware** | [M6 Bolts & Nuts](#mounting-hardware) | eBay (khs_2005) | ✅ Ordered | Stainless steel |

---

## 🏗️ Rack Layout (8U Total)

### Equipment Distribution

| U# | Front Equipment | Rear Equipment | Component Type |
|----|----------------|----------------|----------------|
| **8U** | 🔌 **[Patch Panel](#patch-panel)** | *Free* | 3D Printed + Keystone |
| **7U** | 🌐 **L2/L3 Switch** | *Free* | Hardware (TBD) |
| **6U** | *Free* | *Free* | Future expansion |
| **5U** | *Free* | *Free* | Future expansion |
| **4U** | *Free* | *Free* | Future expansion |
| **3U** | 🖥️ **Mini PC Node #3** | *Free* | Hardware + 3D Mount |
| **2U** | 🖥️ **Mini PC Node #2** | *Free* | Hardware + 3D Mount |
| **1U** | 🖥️ **Mini PC Node #1** | ⚡ **[Power Strip](#power-distribution)** | Hardware + PDU |

### Zones
- **Compute Zone** (1U-3U): Mini PC nodes with custom 3D printed mounts
- **Network Zone** (7U-8U): Switch and patch panel for connectivity
- **Power Zone** (Rear 1U): Centralized power distribution
- **Expansion Zone** (4U-6U): Reserved for future components

---

## 🖨️ 3D Printed Components

### Base Rack System

**Designer**: Michael Klements (mklements)  
**Source**: [Lab-Rax 10" Bolted Version](https://makerworld.com/de/models/1464819-lab-rax-10-server-rack-bolted-version-5u)

| Component | Quantity | Material | Color | Status |
|-----------|----------|----------|-------|--------|
| Base Posts | 4 | PLA Matte | Black |  ✅  Completed |
| Extension Posts | 4 | PLA Matte | Black |  ✅  Completed |
| Side Joiners | 4 | PLA Matte | Black |  ✅  Completed |
| Extention Post Connectors  | 4 | PLA Matte | Black |  ✅  Completed |
| Horizontal Joiners - Solid | 2 | PLA Matte | Black | ⚪ Pending |
| Horizontal Joiners - Grid | 2 | PLA Matte | Orange | ⚪ Pending |
| Handles | 2 | PLA Matte | Orange | ⚪ Pending |
| Foot | 2 | PLA Matte | Orange | ⚪ Pending |

---

### Custom Components

| Component                  | Quantity | Material  | Color        | Purpose                |
|----------------------------|----------|-----------|-------------|------------------------|
| Cooper'n'80s Logo          | 1        | PLA Matte | Orange/Black| Top branding   
| Cooper'n'80s Strip Logo    | 2        | PLA Matte | Orange/Black| Branding for side strips|        |
| Mini PC Mounts             | 3        | PETG      | Black       | Equipment mounting     |
| Switch Mount               | 1        | PETG      | Black       | Equipment mounting     |
| Custom Side Panel (5U)     | 2        | PLA Matte | Black/Orange| Ventilation & branding |
| Custom Side Panel (1.5U)   | 4        | PLA Matte | Black/Orange| Ventilation & branding |


---

### Custom Side Panels

To give the rack a personal touch and improve ventilation, I designed custom side panels inspired by existing community models.

#### Inspiration & Base Work

| Model Name                                   | Author         | Link                                                                 | Notes                       |
|-----------------------------------------------|----------------|----------------------------------------------------------------------|-----------------------------|
| 5U Hexpattern Extension Panel                 | mklements      | [View Model](https://makerworld.com/de/models/1618034-5u-hexpattern-extention-panel#profileId-1708066) | Used as base for 5U panel   |
| 5U Sidepanels with Hex Pattern Ventilation    | mklements      | [View Model](https://makerworld.com/de/models/1577920-5u-sidepanels-with-hex-pattern-ventilation?from=email_notification#profileId-1703572) | Inspiration for ventilation |

#### Modifications

- Since my rack has **8U**, I used the **5U hexpattern** as a base and embedded my own **logo**.  
- I created two additional **1.5U elements** with extra ventilation slots.  
- All modifications were done with **Tinkercad**.  
- In **Bambu Lab Studio**, I applied custom coloring for the final print.

#### Files

The final STL models are stored in the repository under [`assets/`](../assets):

- [`5U_Sidepanel_Logo.stl`](../assets/5U_Sidepanel_Logo.stl)  
- [`1_5U_Sidepanel.stl`](../assets/1_5U_Sidepanel.stl)

#### Preview

| 5U Sidepanel Logo | 1.5U Sidepanel |
|:-----------------:|:--------------:|
| <img src="../../assets/5U_Sidepanel_Logo.png" alt="5U Sidepanel Logo Preview" width="150"/> | <img src="../../assets/1_5U_Sidepanel.png" alt="1.5U Sidepanel Preview" width="150"/> |

*Visual preview of custom side panels used for the Cooper'n'80s rack.*

#### Fun Fact

> As Sheldon Cooper would say:  
> *"It's not just a rack… it's an elegant equation of airflow, symmetry, and personal branding."*

---

### Logos

This section covers all logo elements used for branding the Cooper'n'80s rack, including 3D printed logos for the top rail and long-format strips for exterior mounting.

**Available Assets:**  
- [`Cooper'n80s.stl`](../../assets/Cooper'n80s.stl) — Single color 3D print file  
- [`Cooper'n80s.3mf`](../../assets/Cooper'n80s.3mf) — Dual color 3D print file  
- [`cooper-n-80s.svg`](../../assets/cooper-n-80s.svg) — Vector source (scalable)  
- [`cooper-n-80s_BW.png`](../../assets/cooper-n-80s_BW.png) — Documentation variant  
- [`Logos_long_case.png`](../../assets/Logos_long_case.png) — Preview image for exterior strips  
- [`Cooper'n'80s_long.3mf`](../../assets/Cooper'n%2780s_long.3mf) — 3D print file for exterior strips

**Print Options:**  
- **Single Color:** Black PLA Matte (professional, subtle branding)  
- **Dual Color:** Black base + Orange accent (full brand consistency)

**Mounting Locations:**  
- **Top ventilation rail:** Prominent visibility for 3D logo  
- **Case exterior:** Long-format strips for additional branding

#### Preview

| Cooper'n'80s Logo | Logo Strips (Case Exterior) |
|:----------------------------:|:---------------------------:|
| <img src="../photos/3d-printing/logo_3dprint_preview.png" alt="Logo Preview" width="150"/> | <img src="../../assets/Logos_long_case.png" alt="Logo Strips for Case Exterior" width="300"/> |

*Left: Cooper'n'80s logo for top rail. Right: Screenshot of two long Cooper'n'80s logos intended for exterior mounting.*

---

### Patch Panel

**Source**: [10-Keystone Patch Panel](https://makerworld.com/de/models/907994-patchpanel-for-10-keystones-10-rack)

| Component | Quantity | Material | Color | Notes |
|-----------|----------|----------|-------|-------|
| Panel Frame | 1 | PLA Matte | Black | 1U front mount |
| Keystone Holders | 10 | PLA Matte | Black | Integrated design |

---

## 🔧 Hardware Components

### Networking

**Keystone Jacks**: deleyCON Cat7 Metal Coupling  
**Model**: Keystone-to-Keystone coupler  
**Specs**: 600 MHz, 10 Gbps capability  
**Connector**: 2x RJ45 female  
**Material**: Metal housing  
**Color**: Silver  
**Source**: [deleyCON Official](https://www.deleycon.de/deleycon-cat-7-kupplung-keystone-metall-2x-rj45-buchse-verbinder-fuer-rj45-patchkabel-600-mhz-10-gbps-lan-dsl-ethernet-und-nutzbar-als-keystone-silber/)

---

### Power Distribution

**Model**: DIGITUS DN-95418  
**Type**: 4-outlet power strip  
**Form Factor**: 1HE (1U) rack-mountable  
**Width**: 10-inch (254mm)  
**Color**: Silver/Black  
**Mounting**: Rear rack position  
**Cable**: Integrated power cord

---

### Mounting Hardware

**Bolts**: M6×12mm flanged screws (black zinc plated)  
**Nuts**: M6 hex nuts (stainless steel DIN 934)  
**Quantity**: 100 screws, 50 nuts  
**Source**: eBay (khs_2005)  
**Status**: Ordered (€31.49)

---

## 📐 Assembly Specifications

### Mounting Standards

- **Rack Units**: Standard 1U = 44.45mm (1.75")
- **Mounting Holes**: 10-inch rack standard spacing
- **Bolt Pattern**: M6 metric threading
- **Clearance**: Minimum 15mm between components for airflow

### Cable Management

- **Patch Panel**: Front-to-rear cable routing
- **Power**: Rear-mounted strip with front access
- **Data Cables**: Dedicated cable management clips
- **Service Access**: Tool-free component removal where possible

### Material Specifications

- **Structural Components**: PLA Matte (strength adequate for static loads)
- **Heat-Sensitive Mounts**: PETG (better thermal resistance)
- **Accent Components**: PLA Matte Orange (brand consistency)
- **Hardware**: Stainless steel (corrosion resistance)

---

**Last Updated**: August 17, 2025  
**Total Components**: 15 categories across 4 major systems

> *Precise component specification enables reliable assembly and future maintenance.*
