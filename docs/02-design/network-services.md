# Network Services Architecture

> Enterprise DNS/DHCP foundation for Cooper'n'80s lab infrastructure

## 🎯 Overview

The Cooper'n'80s lab implements enterprise-grade network services providing automatic service discovery, dynamic DNS, and centralized DHCP management. This foundation enables seamless integration of Kubernetes services with existing network infrastructure.

## 🏗️ Service Architecture

### Cooper DNS/DHCP Stack
**Deployment**: Docker Compose orchestrated services at 192.168.1.23
- **PowerDNS Authoritative**: Hosts cooper.lab domain with SQLite backend
- **PowerDNS Recursor**: Provides recursive DNS with intelligent forwarding
- **Kea DHCP4**: Dynamic IP assignment with DDNS integration
- **Dynamic DNS Server**: Automatic DNS record creation from DHCP leases
- **PowerDNS Admin**: Web-based DNS management interface

## 🌐 Network Integration

### Service Discovery Pattern
```
Lab Device Request → DHCP Assignment → DNS Registration → Service Discovery
     ↓                    ↓                ↓                 ↓
10.0.1.x DHCP        IP from pool      A/PTR records    hostname.cooper.lab
```

### Domain Structure
- **cooper.lab**: Authoritative domain for all lab services
- **Automatic Registration**: DHCP leases create DNS records
- **Reverse DNS**: PTR records for proper reverse lookups
- **Upstream Integration**: External queries forwarded to Pi-hole

## 🔧 Operational Integration

### Kubernetes Readiness
**Network Foundation**: DNS infrastructure prepared for K8s services
- **External DNS**: K8s services can register in cooper.lab
- **Service Discovery**: Unified namespace for lab and cluster services
- **Load Balancer Integration**: MetalLB can use cooper.lab domain

### Future Integration Points
- **Ingress Controllers**: Automatic DNS registration for ingress services
- **Service Mesh**: DNS integration for service-to-service communication
- **Monitoring**: Network service health integrated with cluster observability

## 📊 Benefits

### Enterprise Patterns
- **Centralized DNS Management**: Single source of truth for lab networking
- **Automatic Service Registration**: Zero-touch DNS for new services
- **Operational Excellence**: Complete monitoring and troubleshooting procedures
- **Security Integration**: Vault-ready for secrets management

### Learning Value
- **DNS Architecture**: Authoritative vs recursive DNS patterns
- **Dynamic DNS**: DHCP integration with DNS services
- **Service Discovery**: Enterprise networking patterns
- **Container Orchestration**: Multi-service Docker Compose deployment

## 🔄 Integration with Lab Architecture

### Two-Paradigm Support
**Path A (K8s ON Virtualization)**:
- VM hostnames automatically registered in cooper.lab
- K3s services can use ExternalDNS for registration
- Service discovery across VM and container boundaries

**Path B (Virtualization IN K8s)**:
- OKD cluster nodes registered via DHCP
- KubeVirt VMs get automatic DNS registration
- Unified service discovery for all workload types

### Network Topology Integration
- **VLAN 10 (10.0.1.0/24)**: Lab network with automatic DNS
- **Service Network**: cooper.lab provides unified naming
- **Upstream Integration**: Pi-hole handles external resolution
- **Future Expansion**: Ready for additional VLANs and services

---

**Status**: ✅ Operational - Ready for Kubernetes cluster integration  
**Documentation**: Complete operational procedures (private repository)  
**Next Phase**: Mini PC integration and first cluster deployment