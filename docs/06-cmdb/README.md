# CMDB Templates

> Enterprise-grade Configuration Management Database templates for infrastructure documentation

## 🎯 Purpose

These templates demonstrate **enterprise CMDB patterns** used in the Cooper'n'80s infrastructure project. They provide sanitized, reusable examples for:

- **NetBox Migration**: Ready-to-use service definitions
- **Enterprise Documentation**: Professional infrastructure templates
- **Learning Resources**: Real-world CMDB structure examples
- **Compliance Frameworks**: Audit-ready documentation patterns

## 📋 Template Catalog

### Infrastructure Services

| Template | Purpose | Enterprise Use Case |
|----------|---------|-------------------|
| **[vault-service.yaml.template](vault-service.yaml.template)** | HashiCorp Vault secrets management | Enterprise secrets management platforms |
| **[infrastructure-overview.yaml.template](infrastructure-overview.yaml.template)** | Unified platform documentation | Container orchestration platforms |
| **[git-service.yaml.template](git-service.yaml.template)** | Git server and repository management | Version control infrastructure |

### Network Infrastructure

| Template | Purpose | Enterprise Use Case |
|----------|---------|-------------------|
| **[network-infrastructure.yaml.template](../../02-design/network-topology.md)** | Network topology and addressing | Physical and logical network documentation |
| **[hardware-inventory.yaml.template](hardware-inventory.yaml.template)** | Physical hardware tracking | Asset management and lifecycle tracking |

## 🏗️ Template Structure

### Standard Sections

Each template includes enterprise-standard sections:

```yaml
service:
  # Service Identification
  name: "service-identifier"
  display_name: "Human Readable Name"
  service_type: "classification"
  criticality: "business-impact-level"
  
  # Technical Configuration
  deployment: {}      # Platform and deployment info
  network: {}         # Network configuration
  storage: {}         # Data persistence
  security: {}        # Access control and encryption
  
  # Operational Excellence
  backup: {}          # Data protection strategy
  monitoring: {}      # Health checks and metrics
  dependencies: {}    # Service relationships
  integrations: {}    # System integrations
  
  # Change Management
  change_control: {}  # Audit trail and approvals
  roadmap: {}         # Future enhancements
```

### Enterprise Compliance

Templates include fields for:
- **Audit Trails**: Change management and approval workflows
- **Security Documentation**: Access control and encryption standards
- **Business Continuity**: Backup and recovery procedures
- **Performance Monitoring**: SLA tracking and health checks

## 🔧 Usage Instructions

### 1. Template Customization

```bash
# Copy template to your environment
cp vault-service.yaml.template my-vault-service.yaml

# Replace placeholder values
sed -i 's/EXAMPLE/mycompany/g' my-vault-service.yaml
sed -i 's/YYYY-MM-DD/2025-08-20/g' my-vault-service.yaml
```

### 2. NetBox Integration

These templates map directly to NetBox data models:

```python
# NetBox service creation from template
from netbox import NetBoxAPI

# Parse template YAML
service_config = yaml.load(template_file)

# Create NetBox service
service = nb.extras.services.create({
    'name': service_config['service']['name'],
    'protocol': service_config['network']['external_ports'][0]['protocol'],
    'ports': [p['port'] for p in service_config['network']['external_ports']]
})
```

### 3. Infrastructure as Code

Templates integrate with automation tools:

```yaml
# Ansible integration
- name: Deploy service from CMDB template
  include_vars: "{{ service_template }}"
  docker_compose:
    project_name: "{{ service.name }}"
    definition: "{{ service.deployment }}"
```

## 📊 Template Categories

### By Service Type

- **secrets-management**: Vault, key management systems
- **version-control**: Git servers, repository management
- **monitoring**: Prometheus, Grafana, observability
- **orchestration**: Docker Compose, Kubernetes platforms

### By Criticality Level

- **critical**: Zero-downtime requirements, immediate escalation
- **high**: Business-hours support, planned maintenance windows
- **medium**: Best-effort support, extended maintenance windows
- **low**: Minimal support requirements, development services

### By Environment

- **production**: Live customer-facing services
- **staging**: Pre-production validation environment
- **development**: Developer sandbox and testing

## 🔗 Integration Points

### Enterprise Systems

Templates support integration with:

- **NetBox**: Infrastructure documentation and IPAM
- **ServiceNow**: ITSM and change management
- **Terraform**: Infrastructure as Code provisioning
- **Ansible**: Configuration management automation
- **Prometheus**: Monitoring and alerting systems

### Cooper'n'80s Specific

Templates reflect patterns used in:

- **Dual Repository Strategy**: Public learning + private operations
- **Scientific Methodology**: Hypothesis-driven infrastructure decisions
- **Enterprise Standards**: Production-grade patterns at lab scale

## 🎯 Learning Value

### For Students

- **Enterprise Patterns**: Real-world infrastructure documentation
- **Best Practices**: Professional change management and security
- **Tool Integration**: How CMDB connects to automation
- **Compliance**: Audit-ready documentation standards

### For Professionals

- **Template Library**: Reusable patterns for common services
- **Migration Examples**: NetBox and enterprise tool integration
- **Security Standards**: Encryption, access control, key management
- **Operational Excellence**: Monitoring, backup, incident response

## 🛠️ Contributing

### Template Improvements

1. **Fork and modify** templates for your environment
2. **Submit pull requests** with improvements or new patterns
3. **Share experiences** with template usage in different contexts
4. **Suggest new templates** for common infrastructure patterns

### Quality Standards

- **Sanitized Data**: No sensitive information in public templates
- **Enterprise Compliance**: Include all required documentation sections
- **Real-World Tested**: Templates based on actual working infrastructure
- **Documentation**: Clear usage instructions and integration examples

## 📄 License

Templates: MIT License - Free for commercial and personal use  
Documentation: Creative Commons Attribution-ShareAlike 4.0

---

**Template Philosophy**: *"The best infrastructure documentation is the kind that serves both learning and operational excellence - templates that teach while they automate."*

**Cooper'n'80s Integration**: These templates are derived from real operational infrastructure, providing authentic enterprise patterns with educational value.