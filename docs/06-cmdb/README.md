# Dual Repository Strategy

> **S01E04 Update**: Cooper'n'80s now operates with a dual-repository approach for optimal security and learning value.

## 🏗️ Repository Architecture

### 🌐 **cooper-n-80s (Public - GitHub)**
- **Purpose**: Learning documentation, tutorials, and sanitized templates
- **Audience**: Community, learning, knowledge sharing
- **Content**: Architecture decisions, build guides, sanitized examples
- **Security**: No sensitive operational data

### 🔒 **cooper-ops (Private - Self-hosted)**
- **Purpose**: Real operational data and automation
- **Audience**: Internal lab operations only
- **Content**: Actual CMDB data, real configurations, automation scripts
- **Security**: Real MAC addresses, IPs, credentials, procurement details

## 🔄 Workflow Integration

### Data Flow
```
Real Infrastructure → cooper-ops (private CMDB)
                   ↓ sanitization
Template Generation → cooper-n-80s (public docs)
```

### Repository Syncing
- **Templates**: Real CMDB structures → Sanitized public templates
- **Documentation**: Lessons learned → Public knowledge base
- **Code**: Automation scripts (secrets externalized) → Public examples

## 📁 Content Mapping

| Content Type | Public Repo | Private Repo |
|--------------|-------------|--------------|
| **Architecture** | ✅ Full documentation | ⚪ Implementation notes |
| **CMDB Templates** | ✅ Sanitized schemas | ❌ Private |
| **Real CMDB Data** | ❌ Never | ✅ Operational data |
| **Hardware Specs** | ✅ Generic specs | ✅ Serial numbers, MACs |
| **Network Design** | ✅ Topology diagrams | ✅ Real IP addresses |
| **Automation** | ✅ Code examples | ✅ Real configurations |
| **Lessons Learned** | ✅ Community value | ✅ Internal notes |

## 🔐 Security Guidelines

### Never Commit to Public Repo
- Real MAC addresses or serial numbers
- Internal IP addresses and network credentials  
- SSH keys, certificates, or API tokens
- Procurement costs or supplier relationships
- Personal or business-sensitive information

### Safe for Public Repo
- Sanitized configuration templates
- Architecture diagrams and decision rationale
- Generic hardware specifications
- Learning methodologies and best practices
- Troubleshooting guides (with sanitized examples)

## 🛠️ Development Workflow

### Multi-Repository Setup
```bash
# Directory structure
~/cooper-lab/
├── cooper-n-80s/          # Public repository (GitHub)
│   ├── docs/06-cmdb/
│   │   ├── README.md       # CMDB strategy documentation
│   │   ├── templates/      # Sanitized YAML templates
│   │   └── migration/      # NetBox migration guides
│   └── ...
└── cooper-ops/             # Private repository (Self-hosted)
    ├── cmdb/
    │   ├── physical/       # Real hardware inventory
    │   └── logical/        # VM and container tracking
    ├── secrets/            # Encrypted certificates and keys
    └── automation/         # Real Infrastructure as Code
```

### VS Code Workspace
```json
{
  "folders": [
    { "path": "./cooper-n-80s" },
    { "path": "./cooper-ops" }
  ],
  "name": "Cooper'n'80s Lab Workspace"
}
```

## 🎯 Benefits

### **Security First**
- Sensitive operational data properly protected
- Public learning value without operational risk
- Clear separation of concerns

### **Learning Optimized**
- Templates demonstrate enterprise patterns
- Real implementation drives authentic documentation
- Community can benefit from architectural decisions

### **Enterprise Patterns**
- Mirrors real-world public/private repository strategies
- Demonstrates proper data classification
- Shows effective CMDB template development

## 🚀 Getting Started

### For Learning (Public Repository)
```bash
git clone https://github.com/maxsammet/cooper-n-80s.git
cd cooper-n-80s/docs/06-cmdb/
# Review templates and documentation
```

### For Operations (Private Repository)
```bash
# Access requires cooper-ops repository permissions
git clone git@git.sammet.me:maxsammet/cooper-ops.git
cd cooper-ops/cmdb/
# Real operational data and automation
```

---

*"As Sheldon would say: 'The beauty of a properly architected information system is that it provides both transparency for collaboration and confidentiality for operations - like quantum mechanics, but with better documentation.'"*