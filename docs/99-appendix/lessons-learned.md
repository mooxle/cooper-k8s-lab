# Cooper'n'80s - Lessons Learned

> Documenting what actually works vs. what we thought would work.

## 📚 Navigation

| Section | Focus Area | Key Insights |
|---------|------------|--------------|
| **🤖 [GenAI Collaboration](#genai-collaboration-claude-ai)** | AI workflow optimization | Content limits, iteration importance |
| **🔧 [Technical Discoveries](#technical-discoveries)** | Hardware & design findings | Component limitations, design gaps |
| **🔒 [Storage & Network](#storage--network-implementation)** | Enterprise infrastructure | ZFS encryption, VXLAN/EVPN patterns |

## 🤖 GenAI Collaboration (Claude AI)

**Content Limits & RAG Solution**
- Claude hits content limits in long conversations
- Consolidating previous chats via document uploads brings back full context
- RAG (Retrieval Augmented Generation) extends effective conversation length

**Iteration is Critical**
- Important to iterate and verify results
- Problem: MD files not always updated correctly 
- Solution: Follow-up prompts find the problem and restructure properly
- Always check if changes were actually applied

---

## 🔧 Technical Discoveries

**ZFS Encryption Key Format Complexity**
- ZFS raw keys require exactly 32-byte binary format, not ASCII text
- Vault KV v2 stores text - requires conversion: SHA256 hash → xxd binary conversion
- Lesson: Always validate data format requirements when integrating enterprise tools
- Solution: `echo -n "$hash" | xxd -r -p > /etc/zfs/keys/cooper-zfs.key`

**Ansible Task Ordering Dependencies**
- Directory creation must precede file creation operations
- Error: Attempting to create keyfile before /etc/zfs/keys directory exists
- Lesson: Explicit task ordering essential in automation - don't assume implicit dependencies
- Solution: Always create parent directories before dependent resources

**LVM vs ZFS Device Conflicts**
- Cannot create ZFS pool directly on actively used device (nvme0n1p3)
- Root filesystem conflicts with storage pool creation
- Lesson: Active filesystems cannot be modified in-place - requires intermediate layers
- Solution: ZFS-over-LVM logical volume approach provides clean abstraction

**VXLAN Learning vs EVPN Control Plane**
- Traditional bridge learning conflicts with BGP EVPN intelligence
- Bridge flooding interferes with EVPN route exchange
- Lesson: Control plane protocols require data plane cooperation - disable competing mechanisms
- Solution: `bridge-learning off` + `bridge-arp-nd-suppress on` for proper EVPN operation

**BGP EVPN Route Target Configuration**
- EVPN requires explicit route-target for proper route import/export
- Missing RT configuration prevents route propagation
- Lesson: Modern networking protocols have specific configuration requirements
- Solution: `RT:65001:100` for VNI 100 with `advertise-all-vni` in BGP config

**FRR Configuration Persistence**
- FRR requires explicit configuration save operations
- Changes made via vtysh don't automatically persist across reboots
- Lesson: Network daemon configurations need explicit persistence commands
- Solution: Always run `write memory` after BGP configuration changes

**Vault KV v2 API Endpoint Structure**
- KV v2 engines require `/data/` in API paths for CRUD operations
- URI module initially used incorrect KV v1 endpoint format
- Lesson: HashiCorp Vault KV v2 has different API structure than v1
- Solution: Correct path format `/v1/cooper-n-80s/data/{secret-path}` for KV v2

**ZFS Pool Creation Warnings vs Errors**
- "invalid vdev specification" warnings during ZFS pool creation are normal
- Warnings appear when claiming LVM devices but don't indicate failure
- Lesson: Distinguish between warnings and actual errors in ZFS operations
- Solution: Monitor `zpool status` for actual pool health, ignore device claim warnings

**VXLAN MTU Considerations**
- VXLAN adds 50-byte overhead (outer headers + VXLAN header)
- Standard 1500 MTU on underlay works without fragmentation for most workloads
- Lesson: Overlay protocols consume MTU - plan accordingly for jumbo frames if needed
- Solution: Standard MTU sufficient for homelab, jumbo frames available for optimization

**Enterprise Patterns at Homelab Scale**
- Vault, BGP EVPN, ZFS encryption successfully miniaturized from datacenter scale
- Operational complexity minimal with proper automation
- Lesson: Enterprise patterns work at small scale with right tooling approach
- Result: True enterprise capabilities in 3-node lab environment

**Cat7 Keystone Adapters**
- Only available in silver finish
- Cat6a would be available in other colors (including black)
- Trade-off: Performance vs. aesthetics for rack design

**3D Printed Keystone Panel Design Gap**
- Current 3D printable keystone panels have no cable management
- No mounting points for cable ties to secure cables
- Need to develop solution for this

**Rack Layout Optimization**
- 0.5U patch panel enables better equipment spacing
- Mini PCs elevated (2.5U+) for improved thermal management  
- Hidden infrastructure zone (1U-2U) with front covers for professional appearance
- Separation of visible equipment from cable management improves serviceability


**1.5U Panel Height Calculation**
- Custom 1.5U panels have 5mm gap at top - minor measurement discrepancy
- Tinkercad measurements: 1.5U = 66.75mm, 5U = 222.25mm 
- Possible scaling differences between import/export tools
- Functional impact: None - purely aesthetic gap

**Print Orientation Learning**
- Handles require reprinting with corrected orientation
- Wrong orientation resulted in poor surface finish
- Lesson: Test print orientation for non-structural decorative elements
  
**Logo Mounting Orientation Error**
- Hex design crossbar with logo mounted incorrectly - logo was glued instead of properly oriented
- Design has groove for top plate - now positioned on wrong side
- Solution: Reprint required for proper logo integration and structural fit
- Lesson: Carefully check component orientation before assembly, especially branded elements

**Patch Panel Port Calculation**
- 12-port panel over-specified for 8-port switch configuration
- Only need 8 active ports plus 1-2 spares maximum
- Solution: [klayf Keystone Blank Covers](https://makerworld.com/de/models/1265159-keystone-blank-insert-cover-for-petg-pla?from=search#profileId-1293411) in orange PLA
- Result: 4x orange blanks become intentional design feature showing expansion capacity
- Lesson: Over-engineering can become design opportunity with creative solutions

---

*"Every iteration teaches us something new."*

**Document Started**: August 17, 2025