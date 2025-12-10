# Slide Diagrams Reference

These diagrams are from Dex Horthy's "No Vibes Allowed" talk at AI Engineer Code Summit.

---

## Context is Everything
**File:** `Context_is_everything.png`

Shows how the current context (system instructions, CLAUDE.md, tools, messages, tool calls) feeds into the LLM to determine the next step. Illustrates that the context is the ONLY input that influences the output.

**Key Insight:** Every decision the agent makes is based solely on what's in the context window.

---

## Intentional Compaction
**File:** `Compaction_Example.png`

Shows the before/after of intentional compaction:
- **Before:** Long context with many tool calls and messages
- **After:** Fresh context that reads compressed progress.md

**Key Insight:** Compaction resets context utilization while preserving essential state.

---

## Good Compaction Example
**File:** `GREAT_COMPACTION_EXAMPLE.png`

Shows what a good research/compaction output looks like:
- Component Usage Flow with specific paths
- Modal Path (Working) vs Message Stream Path (Broken)
- Exact file:line references
- Code References section

**Key Insight:** Good compaction includes exact files, line numbers, and enough detail to understand the problem without excess verbosity.

---

## Managing Sub-Agents
**File:** `Managing_Sub_Agents.png`

Illustrates how sub-agents work:
- Parent agent spawns Task("Find where XYZ is handled")
- Sub-agent has its own isolated context
- Sub-agent does Read(), Search(), List() operations
- Only the summary ("the file is in src/main/...") returns to parent

**Key Insight:** Sub-agents are for context control, not role-playing. They explore in isolation and return only essential findings.

---

## Detailed Planning
**File:** `Detailed_prompting.png`

Shows the planning workflow structure:
- Step 1: Context Gathering & Initial Analysis
- Step 2: Research & Discovery
- Step 3: Plan Structure Development

**Key Insight:** Good plans have explicit structure with code snippets, verification criteria, and scope boundaries.

---

## Full Plan Example
**File:** `CleanShot_2025-12-10_at_02_00_07_2x.png`

Shows a complete implementation plan including:
- Desired End State section
- What We're NOT Doing section
- Implementation Approach
- Phase breakdown with Success Criteria and code snippets

**Key Insight:** Production-ready plans include actual code, not just descriptions.

---

## Using These Diagrams

These diagrams are useful for:

1. **Training team members** on the methodology
2. **Reference during implementation** to ensure proper structure
3. **Presentations** explaining context engineering concepts
4. **Documentation** of your own workflows based on this methodology

Feel free to use these in your own materials with attribution to Dex Horthy / HumanLayer.
