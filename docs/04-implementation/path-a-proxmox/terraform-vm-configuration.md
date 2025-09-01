# Terraform VM Configuration - Cooper'n'80s

> Infrastructure as Code for automated VM deployment on Proxmox cluster

## 🎯 Overview

Complete Terraform automation for VM lifecycle management on the Cooper'n'80s Proxmox cluster, providing template-driven deployment with network integration and enterprise security patterns.

## 🏗️ Architecture

### Terraform Module Structure
```
terraform/
├── environments/dev/
│   ├── main.tf           # Vault integration and node secrets
│   ├── providers.tf      # Proxmox provider configuration  
│   ├── test-vm.tf        # K3s VM deployment
│   └── variables.tf      # Environment-specific variables
└── modules/proxmox-vm/
    └── main.tf           # Reusable VM module
```

### Network Integration
```
VM Network Flow:
VM (10.0.10.x) → vmbr1 → VXLAN100 → Underlay → Multi-layer routing
                ↓
        DHCP Relay → PowerDNS/Kea → DNS Registration → cooper.lab
```

## 🔧 Proxmox VM Module

### Core Module Configuration
```hcl
# modules/proxmox-vm/main.tf
terraform {
  required_providers {
    proxmox = {
      source  = "bpg/proxmox"
      version = "~> 0.83"
    }
  }
}

# VM Resource Definition
resource "proxmox_virtual_environment_vm" "vm" {
  name      = var.vm_name
  node_name = var.target_node
  vm_id     = var.vm_id

  clone { vm_id = var.template_vmid }

  agent { enabled = true }

  cpu    { cores = var.cores }
  memory { dedicated = var.memory }

  disk {
    datastore_id = var.datastore_id
    size         = var.disk_size_gb
    interface    = "scsi0"
    file_format  = "raw"
  }

  network_device {
    bridge = var.bridge
    model  = "virtio"
  }

  initialization {
    datastore_id = var.datastore_id
    user_data_file_id = proxmox_virtual_environment_file.firstboot_user_data.id

    ip_config {
      ipv4 { address = "dhcp" }
    }
  }

  operating_system { type = "l26" }
  boot_order = ["scsi0", "net0"]

  depends_on = [
    proxmox_virtual_environment_file.firstboot_user_data
  ]
}
```

### Cloud-Init User Data Template
```yaml
# Embedded cloud-config for VM initialization
#cloud-config
hostname: ${var.vm_name}
fqdn: ${var.vm_name}.cooper.lab
manage_etc_hosts: true

users:
  - name: ${var.cloudinit_user}
    groups: [sudo]
    shell: /bin/bash
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
    ssh-authorized-keys:
      - [SSH public keys from variables]

# Network configuration for proper DNS resolution
write_files:
  - path: /etc/systemd/networkd.conf.d/99-clientid-mac.conf
    owner: root:root
    permissions: '0644'
    content: |
      [DHCPv4]
      ClientIdentifier=mac
  - path: /etc/systemd/resolved.conf.d/01-dhcp-only.conf
    owner: root:root
    permissions: '0644'
    content: |
      [Resolve]
      DNS=
      FallbackDNS=

runcmd:
  - truncate -s0 /etc/machine-id || true
  - rm -f /var/lib/dbus/machine-id || true
  - ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
  - systemctl restart systemd-resolved

power_state:
  mode: reboot
  message: "Rebooting for DHCP client-id + machine-id reset"
  timeout: 3
  condition: True
```

## 🌐 Network Configuration Implementation

### Multi-Layer Routing Setup

**Fritz!Box Static Route:**
```
Destination: 10.0.10.0/24
Gateway: 192.168.1.3 (D-Link switch management IP)
```

**D-Link Switch Static Route:**
```
Destination: 10.0.10.0/24  
Gateway: 10.0.1.10 (cooper-node-01)
```

**Proxmox DHCP Relay Configuration:**
```bash
# /etc/default/isc-dhcp-relay (all Proxmox nodes)
SERVERS="10.0.1.23"
INTERFACES="vmbr1 vmbr0"  
OPTIONS=""
```

### DNS/DHCP Extended Configuration

**Kea DHCP4 with Tenant Network:**
```json
{
  "subnet4": [
    {
      "subnet": "10.0.1.0/24",
      "pools": [{ "pool": "10.0.1.10 - 10.0.1.200" }],
      "reservations": [
        { "hw-address": "a4:bb:6d:80:e6:b2", "ip-address": "10.0.1.10", "hostname": "cooper-node-01.cooper.lab" },
        { "hw-address": "a4:bb:6d:80:f0:4f", "ip-address": "10.0.1.11", "hostname": "cooper-node-02.cooper.lab" },
        { "hw-address": "a4:bb:6d:81:56:30", "ip-address": "10.0.1.12", "hostname": "cooper-node-03.cooper.lab" }
      ]
    },
    {
      "subnet": "10.0.10.0/24",
      "pools": [{ "pool": "10.0.10.100 - 10.0.10.199" }],
      "option-data": [
        { "name": "domain-name", "data": "cooper.lab" },
        { "name": "domain-name-servers", "data": "10.0.1.23" },
        { "name": "domain-search", "data": "cooper.lab" },
        { "name": "routers", "data": "10.0.10.1" }
      ]
    }
  ]
}
```

**PowerDNS Recursor Forward Zones:**
```bash
--forward-zones=cooper.lab=172.20.1.10:53,1.0.10.in-addr.arpa=172.20.1.10:53,10.0.10.in-addr.arpa=172.20.1.10:53
--forward-zones-recurse=.=10.0.1.99:53
```

## 🔧 VM Deployment Examples

### K3s High Availability Cluster

**Control Plane Deployment (3 Nodes):**
```hcl
# K3s Control+Worker Node 01
module "k3s_control_01" {
  source      = "../../modules/proxmox-vm"
  vm_name     = "k3s-control-01"
  target_node = "cooper-node-01"
  vm_id       = 401

  cores        = 6
  memory       = 16384
  disk_size_gb = 50
  datastore_id = "cooper-encrypted"
  bridge       = "vmbr1"
  template_vmid = 9000

  cloudinit_user = "maxsammet"
  ssh_keys       = [file("~/.ssh/id_rsa.pub")]
}

# K3s Control+Worker Node 02
module "k3s_control_02" {
  source        = "../../modules/proxmox-vm"
  vm_name       = "k3s-control-02"
  target_node   = "cooper-node-02"
  vm_id         = 402
  cores         = 6
  memory        = 16384
  disk_size_gb  = 50
  datastore_id  = "cooper-encrypted"
  bridge        = "vmbr1"
  template_vmid = 9100

  cloudinit_user = "maxsammet"
  ssh_keys       = [file("~/.ssh/id_rsa.pub")]
}

# K3s Control+Worker Node 03
module "k3s_control_03" {
  source        = "../../modules/proxmox-vm"
  vm_name       = "k3s-control-03"
  target_node   = "cooper-node-03"
  vm_id         = 403
  cores         = 6
  memory        = 16384
  disk_size_gb  = 50
  datastore_id  = "cooper-encrypted"
  bridge        = "vmbr1"
  template_vmid = 9200

  cloudinit_user = "maxsammet"
  ssh_keys       = [file("~/.ssh/id_rsa.pub")]
}
```

### Template Management Script

**Ubuntu Template Creation:**
```bash
#!/usr/bin/env bash
# create-ubuntu-template.sh
# Creates Ubuntu 22.04 Cloud-Init template on Proxmox nodes

# Environment variables for customization
: "${NODE_FQDN:=cooper-node-01.cooper.lab}"
: "${TARGET_NODE_PM:=cooper-node-01}"
: "${VMID:=9000}"
: "${TEMPLATE_NAME:=ubuntu-2204-cloudinit}"
: "${STORAGE:=cooper-encrypted}"
: "${BRIDGE:=vmbr1}"

# Template creation with guest agent integration
qm create "$VMID" --name "$TEMPLATE_NAME" --memory 2048 --cores 2 \
  --net0 "virtio,bridge=$BRIDGE" \
  --serial0 socket --vga serial0 --agent enabled=1

# Import cloud image to encrypted storage
qm importdisk "$VMID" "/root/jammy-server-cloudimg-amd64.img" "$STORAGE"

# Configure VM for cloud-init
qm set "$VMID" --scsihw virtio-scsi-pci
qm set "$VMID" --scsi0 "$STORAGE:vm-$VMID-disk-0"
qm set "$VMID" --ide2 "$STORAGE:cloudinit"
qm set "$VMID" --boot c --bootdisk scsi0

# Convert to template
qm template "$VMID"
```

## 🔧 Technical Challenges & Solutions

### Challenge 1: Hostname/FQDN Configuration
**Problem**: DDNS registration required proper hostname formatting
**Solution**: 
- `hostname`: VM name only (e.g., "k3s-control-01")
- `fqdn`: Full domain (e.g., "k3s-control-01.cooper.lab")
- `manage_etc_hosts: true` for local resolution

### Challenge 2: DNS Resolution Priority  
**Problem**: Cloud-init overwrites resolv.conf
**Solution**:
```bash
# Force systemd-resolved configuration
ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
systemctl restart systemd-resolved
```

### Challenge 3: Guest Agent Installation
**Problem**: Guest tools needed for Proxmox integration
**Solution**: Template-level installation via virt-customize:
```bash
virt-customize -a jammy-server-cloudimg-amd64.img \
  --install qemu-guest-agent \
  --run-command 'systemctl enable qemu-guest-agent'
```

### Challenge 4: Machine-ID Uniqueness
**Problem**: Cloned VMs share machine-id, causing DNS conflicts
**Solution**: Cloud-init machine-id reset:
```bash
truncate -s0 /etc/machine-id || true
rm -f /var/lib/dbus/machine-id || true
# Reboot triggers new machine-id generation
```

### Challenge 5: Network Client Identification
**Problem**: Inconsistent DHCP client identification
**Solution**: Force MAC-based client ID:
```ini
# /etc/systemd/networkd.conf.d/99-clientid-mac.conf
[DHCPv4]
ClientIdentifier=mac
```

## 🚀 Operational Commands

### VM Lifecycle Management
```bash
# Deploy all K3s VMs
cd ~/cooper-lab/terraform/environments/dev
terraform plan
terraform apply

# Destroy for testing/changes
terraform destroy

# Target specific VM
terraform plan -target=module.k3s_control_01
terraform apply -target=module.k3s_control_01
```

### Template Deployment Across Cluster
```bash
# Deploy templates to all nodes with different VMIDs
NODE_FQDN=cooper-node-01.cooper.lab TARGET_NODE_PM=cooper-node-01 VMID=9000 ./create-ubuntu-template.sh
NODE_FQDN=cooper-node-02.cooper.lab TARGET_NODE_PM=cooper-node-02 VMID=9100 ./create-ubuntu-template.sh  
NODE_FQDN=cooper-node-03.cooper.lab TARGET_NODE_PM=cooper-node-03 VMID=9200 ./create-ubuntu-template.sh
```

### Network Validation
```bash
# Test VM connectivity and DNS registration
ping k3s-control-01.cooper.lab
ping k3s-control-02.cooper.lab
ping k3s-control-03.cooper.lab

# Verify DNS records
dig @10.0.1.23 k3s-control-01.cooper.lab A
dig @10.0.1.23 -x 10.0.10.100

# Check DHCP relay status
ansible proxmox_nodes -m shell -a "systemctl status isc-dhcp-relay"
```

## 📊 Resource Allocation Strategy

### K3s High Availability Configuration
```yaml
Cluster Architecture:
  control_plane_nodes: 3
  node_type: "Control+Worker (mixed mode)"
  
per_vm_resources:
  cpu_cores: 6
  memory_gb: 16  
  storage_gb: 50
  network: "vmbr1 (VXLAN overlay)"
  
total_cluster_capacity:
  cpu_total: "18 vCPUs dedicated to K3s HA"
  memory_total: "48GB dedicated to K3s HA"
  expansion_capacity: "18 vCPUs, 48GB available for future workers"
  
estimated_workload_capacity:
  control_plane_overhead: "~6GB (etcd + API servers)"
  system_services: "~6GB (CNI, CSI, monitoring)"
  available_for_workloads: "~36GB effective"
  pod_estimate: "250-350 pods (current HA) → 400-600 (expanded)"
```

### Future Expansion Strategy
```yaml
Phase 1 (Current): 3x Control+Worker VMs
├── Resource: 18 vCPUs, 48GB RAM
├── Capability: True HA with etcd quorum  
├── Workloads: Development, testing, basic production
└── Pod Capacity: 250-350 pods

Phase 2 (Future): + 3x Worker VMs  
├── Additional: 18 vCPUs, 48GB RAM
├── Total: 36 vCPUs, 96GB RAM
├── Workloads: Production databases, CI/CD, analytics
└── Pod Capacity: 400-600 pods
```

## 🔐 Security Implementation

### SSH Key Management
```hcl
# Cloud-init SSH key deployment
ssh-authorized-keys:
  - ssh-rsa AAAAB3NzaC1yc2E... # Public key from file
```

### Network Security
- **VLAN Isolation**: VMs on overlay network (10.0.10.0/24)
- **DNS Integration**: Automatic registration prevents conflicts
- **Service Discovery**: cooper.lab domain provides unified namespace

### User Management
```yaml
# Standard user creation with sudo access
users:
  - name: maxsammet
    groups: [sudo]
    shell: /bin/bash
    sudo: ["ALL=(ALL) NOPASSWD:ALL"]
```

## 🔧 Troubleshooting Lessons Learned

### Cloud-Init Debugging
```bash
# Check cloud-init status on VM
cloud-init status
cloud-init analyze
journalctl -u cloud-init-local.service
journalctl -u cloud-init.service
```

### Network Debugging  
```bash
# Test routing from VM
traceroute 8.8.8.8
ip route show
systemctl status systemd-resolved
```

### Template Issues
```bash
# Verify template availability per node
qm list | grep 9[0-9]00

# Check template configuration
qm config 9000  # Node-01 template
qm config 9100  # Node-02 template  
qm config 9200  # Node-03 template
```

## 🎯 K3s HA Deployment Readiness

### Infrastructure Foundation
- ✅ **VM Automation**: Terraform apply/destroy cycles operational
- ✅ **Network Integration**: Multi-layer routing + DHCP relay working
- ✅ **DNS Registration**: VMs auto-register in cooper.lab domain
- ✅ **Template System**: Ubuntu 22.04 available across all nodes
- ✅ **Security Framework**: SSH key authentication ready

### Next Phase: Ansible K3s Bootstrap
```yaml
K3s HA Deployment Plan:
  approach: "ansible-playbook k3s-ha-bootstrap.yml"
  topology: "3x Control+Worker for true High Availability"
  network: "Calico CNI over VXLAN fabric"
  storage: "ZFS-backed persistent volumes"
  loadbalancer: "MetalLB with cooper.lab DNS integration"
```

---

**Status**: ✅ **OPERATIONAL** - VM automation pipeline complete, K3s HA cluster ready  
**Achievement**: Infrastructure as Code with enterprise network integration  
**Next Phase**: Ansible-automated K3s High Availability cluster deploymenty