# Network Services Operations

> Operational procedures for Cooper'n'80s DNS/DHCP infrastructure (sanitized for public documentation)

## 🎯 Service Overview

The lab uses a comprehensive DNS/DHCP stack deployed in LXC container `ipam.cooper.lab` at **10.0.1.23** providing:
- **Authoritative DNS**: cooper.lab domain management with SQLite backend
- **Recursive DNS**: Full resolution with upstream forwarding to Pi-hole (10.0.1.99)
- **Dynamic DHCP**: Automatic IP assignment for both management and tenant networks
- **Web Management**: PowerDNS Admin at http://10.0.1.23:8082

## 🏗️ Architecture Summary

### Component Stack (LXC Container: ipam.cooper.lab)
- **PowerDNS Authoritative**: Domain authority with database backend (172.20.1.10:53)
- **PowerDNS Recursor**: Client-facing recursive resolver (10.0.1.23:53)
- **Kea DHCP4**: Dynamic IP assignment with DDNS integration
- **DDNS Server**: Automatic DNS record creation
- **Admin Interface**: Web-based management and monitoring

### Network Integration
```
Lab Networks (served by ipam.cooper.lab):
├── Management: 10.0.1.0/24 → DHCP Pool: 10.0.1.100-200
├── Tenant: 10.0.10.0/24 → DHCP Pool: 10.0.10.100-200
├── Gateway: 10.0.1.1 (router VLAN interface)
├── DNS Resolution: cooper.lab + upstream to Pi-hole
└── Service Discovery: Automatic registration for all devices
```

### DNS Zones Configured
- **Forward Zone**: cooper.lab (authoritative)
- **Reverse Zones**: 
  - 1.0.10.in-addr.arpa (10.0.1.0/24)
  - 10.0.10.in-addr.arpa (10.0.10.0/24)

### Key Configuration Elements
```yaml
# Recursor Forward Zones
forward-zones: |
  cooper.lab=172.20.1.10:53
  1.0.10.in-addr.arpa=172.20.1.10:53
  10.0.10.in-addr.arpa=172.20.1.10:53
forward-zones-recurse: .=10.0.1.99:53

# DHCP Reservations (Static assignments for reliable boot)
reservations:
  - hw-address: "a4:bb:6d:80:e6:b2"
    ip-address: "10.0.1.10"
    hostname: "cooper-node-01.cooper.lab"
  - hw-address: "a4:bb:6d:80:f0:4f"
    ip-address: "10.0.1.11"
    hostname: "cooper-node-02.cooper.lab"
  - hw-address: "a4:bb:6d:81:56:30"
    ip-address: "10.0.1.12"
    hostname: "cooper-node-03.cooper.lab"
```
```

## 🔧 Essential Operations

### Service Management
```bash
# Start/stop services
docker compose up -d
docker compose down

# Health checks
docker compose ps
docker compose logs -f [service]

# Restart specific service
docker compose restart [service-name]
```

### DNS Operations
```bash
# Test local resolution
dig @[dns-server] hostname.cooper.lab A
dig @[dns-server] -x [ip-address]

# External resolution test
dig @[dns-server] google.com A

# Check service status
curl -s http://[dns-server]:8083/api/v1/servers/localhost/statistics
```

### DHCP Monitoring
```bash
# View active leases
[lease-display-script]

# Monitor assignments
docker compose logs kea-dhcp4 --since=1h | grep "lease"

# Check DHCP server status
docker exec [container] keactrl status
```

## 📋 Operational Procedures

### Daily Health Checks
- **Container Status**: Verify all services running
- **DNS Resolution**: Test both local and external queries
- **DHCP Leases**: Monitor active assignments
- **Service Discovery**: Validate automatic registration

### Weekly Maintenance
- **Database Optimization**: SQLite vacuum and maintenance
- **Configuration Validation**: Verify compose file and configs
- **Log Management**: Rotation and cleanup procedures
- **Performance Monitoring**: Resource usage and query statistics

### Monthly Operations
- **Security Updates**: Update container images
- **Capacity Planning**: Monitor resource and address pool usage
- **Backup Validation**: Test database and configuration backups

## 🔍 Troubleshooting Framework

### DNS Resolution Issues
```bash
# Test resolution chain
dig @[authoritative] hostname.cooper.lab A    # Direct authoritative test
dig @[recursor] hostname.cooper.lab A         # Client perspective test
dig @[recursor] google.com A                  # External resolution test

# Check forwarding configuration
docker exec [recursor] rec_control get-all | grep forward
```

### DHCP Assignment Problems
```bash
# Check lease database
docker exec [dhcp-container] cat [lease-file]

# Monitor DHCP traffic
docker compose logs kea-dhcp4 --since=10m

# Verify network connectivity
docker exec [dhcp-container] ping [gateway]
```

### Dynamic DNS Failures
```bash
# Check DDNS server logs
docker compose logs kea-ddns --since=1h

# Verify API connectivity  
curl -H "X-API-Key: [key]" http://[auth-server]:8081/api/v1/servers/localhost

# Manual DNS record test
[manual-record-creation-command]
```

## 🔒 Security Considerations

### Access Control
- **API Security**: Secure key management and rotation
- **Network Binding**: Service accessibility controls
- **Container Security**: Proper user permissions and isolation

### Data Protection
- **Database Security**: SQLite file permissions and encryption
- **Backup Security**: Encrypted backups with secure storage
- **Log Security**: Sensitive information filtering in logs

### Network Security
- **Firewall Rules**: Restrict access to management interfaces
- **VLAN Isolation**: Proper network segmentation
- **Upstream Security**: Secure communication with upstream DNS

## 📊 Performance Monitoring

### Key Metrics
- **DNS Query Rate**: Queries per second and response times
- **DHCP Pool Utilization**: Active leases vs available addresses
- **Container Resources**: CPU, memory, and disk usage
- **Database Performance**: SQLite query performance and size

### Alerting Framework
- **Service Availability**: Container and network service health
- **Resource Thresholds**: Memory, CPU, and disk space limits
- **Configuration Drift**: Changes to critical configuration files
- **Security Events**: Unauthorized access attempts or configuration changes

## 🔗 Integration Points

### Kubernetes Integration
**External DNS**: Automatic registration of K8s services in cooper.lab
- **Ingress Services**: HTTP/HTTPS services get automatic DNS entries
- **LoadBalancer Services**: MetalLB integration with DNS registration
- **Service Discovery**: Cross-cluster service communication via DNS

### Monitoring Integration
**Observability Stack**: Integration with Prometheus/Grafana
- **DNS Metrics**: Query rates, response times, error rates
- **DHCP Metrics**: Pool utilization, lease duration statistics
- **Container Metrics**: Resource usage and health status

### Vault Integration
**Secrets Management**: HashiCorp Vault for sensitive configuration
- **API Keys**: Secure storage and rotation of DNS API keys
- **Certificates**: SSL certificate management for web interfaces
- **Database Credentials**: Encrypted storage of database connections

---

**Note**: This is a sanitized overview for learning purposes. Complete operational procedures with specific configurations, credentials, and detailed troubleshooting workflows are maintained in private documentation for security reasons.

**Status**: ✅ Operational - Enterprise-grade DNS/DHCP infrastructure ready for Kubernetes integration  
**Learning Value**: Demonstrates enterprise network service patterns and operational excellence practices