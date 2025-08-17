# Network Topology - Cooper'n'80s K8s Lab

> Physical and logical network architecture visualization

## ⚠️ DISCLAIMER: Planning Phase

**IMPORTANT:** This document represents the **planned network architecture** for the Cooper'n'80s K8s Lab. The actual implementation may differ as the project evolves and hardware becomes available.

**Current Status:**
- ✅ D-Link DGS-1100-08V2 switch purchased
- ✅ VLAN 10 configured on home network
- ⏳ Mini PCs not yet acquired
- ⏳ Proxmox/K3s setup pending hardware
- ⏳ RPi gateway optional/future consideration

This topology serves as a **blueprint and reference** for the intended setup, documenting decisions made during the planning phase.

---

## 📡 Physical Network Topology

### Standard Home Lab Configuration

```
                        INTERNET
                            │
                    ┌───────┴───────────────┐
                    │  Fritz!Box Router     │
                    │  192.168.1.1           │
                    │  Gateway/NAT/Firewall  │
                    └───────┬────────────────┘
                            │ Native Network
                            │ 192.168.1.0/24
                            │
                    ┌───────▼───────────────┐
                    │  D-Link DGS-1210      │
                    │  (Home Core Switch)   │
                    │                       │
                    │  Services:            │
                    │  • Pi-hole DHCP/DNS   │
                    │    192.168.1.99       │
                    │  • Home devices       │
                    │  • VLAN 10 routing    │
                    │                       │
                    │  Port 18: VLAN 10     │
                    │  Untagged             │
                    └───────┬───────────────┘
                            │ Untagged VLAN 10
                            │ 10.0.1.0/24
                            │
                    ┌───────▼───────┐
              Port 1│               │Port 8
         Uplink────►│  D-Link       │◄──── Management/Laptop
         VLAN 10    │  DGS-1100-08  │       10.0.1.100
         Untagged   │  10.0.1.2     │       (DHCP/Static)
                    │               │
                    │ L2 Switch     │
                    └─┬──┬──┬──┬──┬─┘
                      │  │  │  │  │
                 Port 2  3  4  5  6-7
                      │  │  │  │  │
                 Untagged VLAN 10  Reserve
                      │  │  │
                      ▼  ▼  ▼
          ┌─────────┐ ┌─────────┐ ┌─────────┐
          │Proxmox 1│ │Proxmox 2│ │Proxmox 3│
          │Node     │ │Node     │ │Node     │
          │10.0.1.10│ │10.0.1.11│ │10.0.1.12│
          │         │ │         │ │         │
          │ K3s VM  │ │ K3s VM  │ │ K3s VM  │
          │ Control │ │ Worker  │ │ Worker  │
          └─────────┘ └─────────┘ └─────────┘
           Mini PC 1   Mini PC 2   Mini PC 3
```

### Port Assignment Table

```
┌──────┬─────────────────┬──────────────┬──────────────────┐
│ Port │ Device          │ IP Address   │ Configuration    │
├──────┼─────────────────┼──────────────┼──────────────────┤
│  1   │ Uplink          │ -            │ Untagged VLAN 10 │
│  2   │ Proxmox Node 1  │ 10.0.1.10    │ Untagged VLAN 10 │
│  3   │ Proxmox Node 2  │ 10.0.1.11    │ Untagged VLAN 10 │
│  4   │ Proxmox Node 3  │ 10.0.1.12    │ Untagged VLAN 10 │
│  5   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  6   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  7   │ Reserved        │ DHCP         │ Untagged VLAN 10 │
│  8   │ Admin/Laptop    │ 10.0.1.100   │ Untagged VLAN 10 │
└──────┴─────────────────┴──────────────┴──────────────────┘
```

---

## 🚀 Portable Configuration (with RPi Gateway)

### Mobile Lab Setup

```
                    EXTERNAL NETWORK
                    (Hotel/Office/etc)
                            │
                            │ DHCP Client
                            │
                    ┌───────▼───────┐
                    │  Raspberry Pi │ eth0: DHCP from external
                    │  Gateway      │ eth1: 10.0.1.1 (static)
                    │               │
                    │  NAT/Firewall │
                    └───────┬───────┘
                            │ 10.0.1.0/24
                            │ (Internal Network)
                            │
                    ┌───────▼───────┐
              Port 1│               │Port 8
           From RPi─►│  D-Link       │◄──── Management/Laptop
           10.0.1.1  │  DGS-1100-08  │       10.0.1.100
                    │  10.0.1.2     │
                    │               │
                    └─┬──┬──┬──┬──┬─┘
                      │  │  │  │  │
                      ▼  ▼  ▼  ▼  ▼
                   K8s Nodes (unchanged)
```

### RPi Gateway Configuration

```
┌─────────────────────────────────────┐
│         Raspberry Pi 4B             │
├─────────────────────────────────────┤
│ eth0 (External):                    │
│   - DHCP Client                     │
│   - Gets IP from venue network      │
│                                     │
│ eth1 (Internal via USB adapter):    │
│   - Static IP: 10.0.1.1/24         │
│   - DHCP Server for K8s nodes      │
│                                     │
│ Services:                           │
│   - NAT/Masquerade                 │
│   - DHCP Server (10.0.1.10-200)    │
│   - DNS Forwarding                 │
│   - Optional: VPN Server           │
└─────────────────────────────────────┘
```

---

## 🌐 Logical Network Layers

### Network Stack Overview

```
┌─────────────────────────────────────────────────┐
│                 Application Layer                │
│         Kubernetes Services & Ingress            │
├─────────────────────────────────────────────────┤
│              Container Network (CNI)             │
│          Calico/Flannel VXLAN Overlay           │
│               Pod Network: 172.16.0.0/16        │
├─────────────────────────────────────────────────┤
│            Virtualization Layer (Proxmox)        │
│              VXLAN/EVPN for VM Networks         │
│            VM Networks: 10.0.2.0/24+            │
├─────────────────────────────────────────────────┤
│              Physical Network (L2/L3)            │
│          Management: 10.0.1.0/24 (VLAN 10)      │
│                 D-Link Switch L2                 │
├─────────────────────────────────────────────────┤
│                Physical Hardware                 │
│         3x Mini PCs + Switch + Cables           │
└─────────────────────────────────────────────────┘
```

---

## 🔌 Cable Layout in 8U Rack

### Physical Rack Wiring

```
    ╔══════════════════════════════╗
1U  ║     Patch Panel (Future)     ║
    ╠══════════════════════════════╣
2U  ║    D-Link DGS-1100-08       ║
    ║  [1][2][3][4][5][6][7][8]   ║
    ╠══════════════════════════════╣
3U  ║    RPi + Management Tools    ║
    ╠══════════════════════════════╣
4U  ║    Mini PC Node 1 (Control)  ║──┐
    ╠══════════════════════════════╣  │ 0.5m cables
5U  ║    Mini PC Node 2 (Worker)   ║──┤ Orange
    ╠══════════════════════════════╣  │
6U  ║    Mini PC Node 3 (Worker)   ║──┘
    ╠══════════════════════════════╣
7U  ║    1U Power Distribution     ║
    ╠══════════════════════════════╣
8U  ║    Reserve / Storage         ║
    ╚══════════════════════════════╝

Cable Lengths:
- Switch → Node 1: 0.25m
- Switch → Node 2: 0.5m
- Switch → Node 3: 0.5m
- Switch → RPi: 0.25m
- Uplink: 1-2m to home router
```

---

## 📊 IP Address Allocation

### Static Assignments (DHCP Reservations)

```
Network: 10.0.1.0/24
Gateway: 10.0.1.1 (DGS-1210 VLAN interface)

┌────────────────┬─────────────┬─────────────────────────────┐
│ IP Address     │ Device      │ Purpose                     │
├────────────────┼─────────────┼─────────────────────────────┤
│ 10.0.1.1       │ DGS-1210    │ VLAN 10 Gateway            │
│ 10.0.1.2       │ DGS-1100-08 │ Switch Management IP       │
│ 10.0.1.10      │ Proxmox 1   │ Mini PC 1 - K3s Control    │
│ 10.0.1.11      │ Proxmox 2   │ Mini PC 2 - K3s Worker     │
│ 10.0.1.12      │ Proxmox 3   │ Mini PC 3 - K3s Worker     │
│ 10.0.1.20-29   │ Reserved    │ Future Nodes               │
│ 10.0.1.30-49   │ Reserved    │ Services/LB IPs            │
│ 10.0.1.50-99   │ Reserved    │ Infrastructure             │
│ 10.0.1.100     │ Admin Laptop│ Management Access          │
│ 10.0.1.101-200 │ DHCP Pool   │ Dynamic Clients            │
└────────────────┴─────────────┴─────────────────────────────┘
```

---

## 🔄 Traffic Flow Examples

### Pod-to-Pod Communication

```
Pod A (Node 1)          Pod B (Node 2)
172.16.1.10            172.16.2.20
     │                      │
     ▼                      ▼
Calico VXLAN           Calico VXLAN
     │                      │
     ▼                      ▼
Node 1 (10.0.1.10) ───► Node 2 (10.0.1.11)
     │                      │
     └──────┬───────────────┘
            ▼
     D-Link Switch (L2)
```

### External Service Access

```
Internet Request
       │
       ▼
Home Router (NAT)
       │
    VLAN 10
       │
       ▼
D-Link Switch
       │
       ▼
Node 1 (Control)
       │
       ▼
Ingress Controller
       │
       ▼
Service Pod
```

---

## 🛠️ Configuration Commands

### D-Link Switch Initial Setup

```bash
# Access switch web interface
http://10.0.1.2

# Configure as access switch (all ports untagged VLAN 10)
- Port 1: Untagged member of VLAN 10 (from DGS-1210 port 18)
- Ports 2-8: Untagged members of VLAN 10
- PVID for all ports: 10
- Management VLAN: 10
```

### RPi Gateway Setup (Portable Mode)

```bash
# /etc/netplan/01-netcfg.yaml (Ubuntu)
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
    eth1:
      addresses: [10.0.1.1/24]
      
# Enable NAT
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT

# DHCP Server (dnsmasq)
interface=eth1
dhcp-range=10.0.1.10,10.0.1.200,255.255.255.0,12h
dhcp-option=3,10.0.1.1  # Gateway
dhcp-option=6,8.8.8.8   # DNS
```

---

**Network Philosophy:** *"A well-planned topology is half the battle won. The other half is good cable management!"* 🔌✨