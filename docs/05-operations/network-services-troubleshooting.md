# Network Services Troubleshooting Guide

> Comprehensive troubleshooting procedures for Cooper'n'80s DNS/DHCP infrastructure

## 🎯 Overview

This guide provides systematic troubleshooting procedures for the PowerDNS + Kea DHCP stack, based on real-world debugging sessions and resolution patterns.

## 🔧 Common Issues and Solutions

### Issue 1: PowerDNS Recursor Forward Zones Not Working

**Symptom:**
```bash
# Direct authoritative queries work
dig @172.20.1.10 max-air-m3-ddns.cooper.lab A
# Returns: 10.0.1.100

# Recursor queries fail  
dig @192.168.1.23 max-air-m3-ddns.cooper.lab A
# Returns: NXDOMAIN
```

**Root Cause Analysis:**

#### Step 1: Check Recursor Configuration
```bash
# Check if forward zones are configured
docker exec cooper-recursor rec_control get-all | grep forward

# Expected output:
# forward-zones=cooper.lab=172.20.1.10:53;1.0.10.in-addr.arpa=172.20.1.10:53
# forward-zones-recurse=.=192.168.1.99:53

# If empty, configuration is not applied
```

#### Step 2: Verify Environment Variables
```bash
# Check container environment
docker exec cooper-recursor env | grep PDNS

# Should show:
# PDNS_RECURSOR_FORWARD_ZONES=cooper.lab=172.20.1.10:53;1.0.10.in-addr.arpa=172.20.1.10:53
# PDNS_RECURSOR_FORWARD_ZONES_RECURSE=.=192.168.1.99:53
```

#### Step 3: Identify Config File Override Issue
```bash
# Check if config file is mounted
docker inspect cooper-recursor | grep -A 10 -B 10 Mounts

# If recursor.conf is mounted, it overrides environment variables
# This is the most common cause of forward zone configuration issues
```

**Solution A: Remove Config File Mount**
```yaml
# In docker-compose.yml, remove or comment out:
# volumes:
#   - ./config/recursor.conf:/etc/powerdns/recursor.conf:ro

# Use environment variables or command line args instead
powerdns-recursor:
  image: powerdns/pdns-recursor-49:latest
  environment:
    PDNS_RECURSOR_LOCAL_ADDRESS: "0.0.0.0"
    PDNS_RECURSOR_ALLOW_FROM: "10.0.1.0/24,192.168.1.0/24,172.20.1.0/24"
    PDNS_RECURSOR_FORWARD_ZONES: "cooper.lab=172.20.1.10:53;1.0.10.in-addr.arpa=172.20.1.10:53"
    PDNS_RECURSOR_FORWARD_ZONES_RECURSE: ".=192.168.1.99:53"
    PDNS_RECURSOR_WEBSERVER: "yes"
    PDNS_RECURSOR_WEBSERVER_ADDRESS: "0.0.0.0"
    PDNS_RECURSOR_WEBSERVER_PORT: "8082"
```

**Solution B: Use Command Line Arguments**
```yaml
powerdns-recursor:
  image: powerdns/pdns-recursor-49:latest
  command: >
    --local-address=0.0.0.0
    --allow-from=10.0.1.0/24,192.168.1.0/24,172.20.1.0/24
    --forward-zones=cooper.lab=172.20.1.10:53;1.0.10.in-addr.arpa=172.20.1.10:53
    --forward-zones-recurse=.=192.168.1.99:53
    --serve-rfc1918=no
    --webserver=yes --webserver-address=0.0.0.0 --webserver-port=8082
    --control-enable=yes
```

#### Step 4: Recreate Container
```bash
# Remove and recreate recursor
docker compose rm -sf powerdns-recursor
docker compose up -d powerdns-recursor

# Verify configuration is applied
docker exec cooper-recursor rec_control get-all | grep forward
```

#### Step 5: Validate Resolution
```bash
# Test forward zone resolution
dig @192.168.1.23 max-air-m3-ddns.cooper.lab A

# Test reverse zone resolution  
dig @192.168.1.23 -x 10.0.1.100

# Both should now return correct answers
```

### Issue 2: DNS Cache Problems

**Symptom:** Changes to DNS records not reflected in queries

**Solution:**
```bash
# Clear recursor cache
docker exec cooper-recursor rec_control wipe-cache

# Clear authoritative cache (if applicable)
docker exec cooper-powerdns pdns_control purge cooper.lab

# Restart DNS services if needed
docker compose restart powerdns-recursor cooper-powerdns
```

### Issue 3: DHCP DDNS Updates Not Working

**Symptom:** DHCP assigns IPs but DNS records are not created

**Troubleshooting:**
```bash
# Check Kea DHCP logs
docker compose logs kea-dhcp4 | grep -i ddns

# Check DDNS server logs  
docker compose logs kea-dhcp-ddns

# Verify DDNS server connectivity to PowerDNS
docker exec kea-dhcp-ddns nc -zv 172.20.1.10 53
```

**Common Solutions:**
```bash
# Restart DDNS services in correct order
docker compose stop kea-dhcp-ddns kea-dhcp4
docker compose start kea-dhcp-ddns
sleep 5
docker compose start kea-dhcp4

# Check DDNS API key configuration
docker exec cooper-powerdns pdns_control show-zone cooper.lab
```

### Issue 4: Container Networking Problems

**Symptom:** Services can't communicate within Docker network

**Diagnostics:**
```bash
# Check Docker network
docker network ls
docker network inspect cooper-dns_cooper-dns

# Test container-to-container connectivity
docker exec cooper-recursor ping 172.20.1.10
docker exec cooper-recursor nslookup cooper-powerdns

# Check service listening ports
docker exec cooper-powerdns netstat -tulpn | grep :53
docker exec cooper-recursor netstat -tulpn | grep :53
```

### Issue 5: External Resolution Not Working

**Symptom:** Internal domains work, but external queries fail

**Troubleshooting:**
```bash
# Test external resolution directly
docker exec cooper-recursor dig @192.168.1.99 google.com

# Check forward-zones-recurse configuration
docker exec cooper-recursor rec_control get-all | grep "forward-zones-recurse"

# Should show: forward-zones-recurse=.=192.168.1.99:53
```

## 🔍 Systematic Debugging Approach

### Step 1: Service Status Check
```bash
# Check all container status
docker compose ps

# Check service health
docker compose logs --tail=50 cooper-recursor
docker compose logs --tail=50 cooper-powerdns
docker compose logs --tail=50 kea-dhcp4
```

### Step 2: Network Connectivity
```bash
# Test network reachability
ping 192.168.1.23  # Recursor
ping 172.20.1.10   # Authoritative
ping 192.168.1.99  # Upstream DNS

# Test service ports
nc -zv 192.168.1.23 53    # Recursor DNS
nc -zv 172.20.1.10 53     # Authoritative DNS
nc -zv 192.168.1.23 8082  # Recursor web
```

### Step 3: Configuration Validation
```bash
# Verify recursor runtime config
curl -H "X-API-Key: YOUR_API_KEY" \
  http://192.168.1.23:8082/api/v1/servers/localhost/config | \
  jq '.forward_zones'

# Verify authoritative zones
curl -H "X-API-Key: YOUR_API_KEY" \
  http://172.20.1.10:8081/api/v1/servers/localhost/zones
```

### Step 4: Query Flow Testing
```bash
# Test complete query flow
dig +trace @192.168.1.23 max-air-m3-ddns.cooper.lab

# Test direct authoritative
dig @172.20.1.10 max-air-m3-ddns.cooper.lab

# Test upstream forwarding
dig @192.168.1.23 google.com
```

## 🛠️ Recovery Procedures

### Complete Service Restart
```bash
# Stop all services
docker compose down

# Remove containers (keeps volumes)
docker compose rm -f

# Start services in correct order
docker compose up -d db-init
sleep 5
docker compose up -d cooper-powerdns
sleep 5  
docker compose up -d powerdns-recursor
sleep 5
docker compose up -d kea-dhcp-ddns kea-dhcp4
sleep 5
docker compose up -d powerdns-admin
```

### Database Cleanup (if needed)
```bash
# Backup database
cp /path/to/powerdns.db /path/to/powerdns.db.backup

# Clean and reinitialize
docker compose down
docker volume rm cooper-dns_db-data
docker compose up -d db-init
```

### Configuration Reset
```bash
# Backup current config
cp compose.yml compose.yml.backup

# Reset to known working configuration
git checkout HEAD -- compose.yml

# Apply configuration and restart
docker compose down
docker compose up -d
```

## 📊 Monitoring and Health Checks

### Automated Health Checks
```bash
#!/bin/bash
# dns-health-check.sh

# Test recursor
dig @192.168.1.23 google.com +short || echo "Recursor external resolution failed"

# Test authoritative  
dig @172.20.1.10 cooper.lab SOA +short || echo "Authoritative not responding"

# Test DHCP/DDNS integration
dhcp_lease_count=$(grep -c "lease" /var/lib/dhcp/dhcpd.leases)
dns_record_count=$(dig @192.168.1.23 cooper.lab AXFR | grep -c "IN A")
echo "DHCP leases: $dhcp_lease_count, DNS A records: $dns_record_count"
```

### Log Monitoring
```bash
# Monitor critical errors
docker compose logs -f | grep -E "(ERROR|FAILED|refused)"

# Monitor DDNS activity
docker compose logs -f kea-dhcp-ddns | grep -E "(UPDATE|RESPONSE)"

# Monitor query patterns
docker compose logs -f powerdns-recursor | grep -E "(query|answer)"
```

## 📋 Prevention Best Practices

### Configuration Management
- ✅ **Version Control**: Keep all configs in Git
- ✅ **Environment Variables**: Use ENV over mounted config files  
- ✅ **Validation**: Test config changes in isolation
- ✅ **Documentation**: Document all custom configurations

### Monitoring Setup
- ✅ **Health Checks**: Regular automated validation
- ✅ **Log Aggregation**: Centralized logging and alerting
- ✅ **Performance Monitoring**: Query response times and error rates
- ✅ **Capacity Planning**: Monitor resource usage trends

---

**Status**: ✅ **COMPREHENSIVE** - Based on real troubleshooting experience  
**Integration**: Works with Cooper'n'80s DNS/DHCP infrastructure  
**Maintenance**: Updated based on operational experience and new issues