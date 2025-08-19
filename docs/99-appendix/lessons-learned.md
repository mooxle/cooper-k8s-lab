# Cooper'n'80s - Lessons Learned

> Documenting what actually works vs. what we thought would work.

## 📚 Navigation

| Section | Focus Area | Key Insights |
|---------|------------|--------------|
| **🤖 [GenAI Collaboration](#genai-collaboration-claude-ai)** | AI workflow optimization | Content limits, iteration importance |
| **🔧 [Technical Discoveries](#technical-discoveries)** | Hardware & design findings | Component limitations, design gaps |

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