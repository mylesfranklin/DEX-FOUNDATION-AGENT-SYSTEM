# The Research Phase

## Purpose

The Research phase is about **understanding how the system works** before making any changes. It produces compressed, accurate documentation of the relevant parts of the codebase.

### Goals
- Find the right files
- Understand code flow
- Stay objective (no implementation yet)
- Produce a reusable research document

---

## Research Output: What Good Looks Like

A good research document contains **exact files and line numbers** relevant to the problem:

```markdown
## Component Usage Flow

### Modal Path (Working)
1. `ToolResultModal.tsx:136` calls `getToolIcon(toolCall?.toolName)`
2. `getToolIcon()` returns `<Terminal />` for Bash
3. Icon displays correctly with terminal symbol

### Message Stream Path (Broken)
1. `ConversationContent.tsx:103` calls `eventToDisplayObject(event)`
2. Default `<Wrench />` assigned at line 76
3. No Bash-specific override in lines 189-228
4. Generic wrench icon displays

## Code References
- `humanlayer-wui/src/components/internal/SessionDetail/eventToDisplayObject.tsx:76` - Default wrench assignment
- `humanlayer-wui/src/components/internal/SessionDetail/eventToDisplayObject.tsx:189-228` - Tool-specific overrides (missing Bash)
- `humanlayer-wui/src/components/internal/SessionDetail/eventToDisplayObject.tsx:657` - Correct Bash→Terminal mapping
```

Notice:
- Specific file paths with line numbers
- Clear description of what each reference does
- Identifies the problem location precisely
- No implementation suggestions yet

---

## Research Workflow

### Step 1: Context Gathering & Initial Analysis

**Read all mentioned files immediately and FULLY:**
- Ticket files (e.g., `thoughts/allison/tickets/eng_1234.md`)
- Research documents
- Related implementation plans
- Any JSON/data files mentioned

**IMPORTANT:** Use the Read tool WITHOUT limit/offset parameters to read entire files.

**CRITICAL:** DO NOT spawn sub-tasks before reading files yourself in the main context.

### Step 2: Research & Discovery

After getting initial clarifications:

**If the user corrects any misunderstanding:**
- DO NOT just accept the correction
- Spawn new research tasks to verify the correct information
- Read the specific files/directories they mention
- Only proceed once you've verified the facts yourself

**Spawn initial research tasks to gather context:**

Before asking the user any questions, use specialized agents to research in parallel:
- Use the `codebase-locator` agent to find all files related to the ticket/task
- Use the `codebase-analyzer` agent to understand how the current implementation works
- If relevant, use the `thoughts-locator` agent to find any existing thoughts documents
- If a Linear ticket is mentioned, use the `linear-ticket-reader` agent to get full details

### Step 3: Present Findings and Design Options

Wait for ALL sub-tasks to complete before proceeding.

**Present findings and design options:**

```markdown
Based on my research, here's what I found:

**Current State:**
- [Key discovery about existing code]
- [Pattern or convention to follow]

**Design Options:**
1. [Option A] - [pros/cons]
2. [Option B] - [pros/cons]

**Open Questions:**
- [Technical uncertainty]
- [Design decision needed]

Which approach aligns best with your vision?
```

---

## Research Document Template

```markdown
---
date: [Current date and time with timezone in ISO format]
researcher: [Researcher name]
git_commit: [Current commit hash]
branch: [Current branch name]
repository: [Repository name]
topic: "[User's Question/Topic]"
tags: [research, codebase, relevant-component-names]
status: complete
last_updated: [Current date in YYYY-MM-DD format]
last_updated_by: [Researcher name]
---

# Research: [User's Question/Topic]

**Date**: [Current date and time with timezone]
**Researcher**: [Researcher name]
**Git Commit**: [Current commit hash]
**Branch**: [Current branch name]
**Repository**: [Repository name]

## Research Question
[Original user query]

## Summary
[High-level findings answering the user's question]

## Detailed Findings

### [Component/Area 1]
- Finding with reference ([file.ext:line](link))
- Connection to other components
- Implementation details

### [Component/Area 2]
- Finding with reference ([file.ext:line](link))
- Connection to other components
- Implementation details

## Code References
- `path/to/file.tsx:123` - Description of what this does
- `path/to/file.tsx:456-789` - Description of what this range does

## Related Research
- [Link to related research if any]

## Next Steps
- [Suggested next actions]
```

---

## Research Prompt Template

```markdown
# Research Task

## Objective
Understand how [SYSTEM/FEATURE] works in order to [GOAL].

## Focus Areas
- [Area 1 to investigate]
- [Area 2 to investigate]
- [Area 3 to investigate]

## Instructions
1. Find all files related to [TOPIC]
2. Trace the code flow from [ENTRY POINT] to [END POINT]
3. Document key functions and their responsibilities
4. Identify patterns and conventions used
5. Note any edge cases or gotchas

## Output Format
Create a research document at `thoughts/shared/research/[topic].md` with:
- Summary of findings
- Detailed code references with line numbers
- Diagram of data/control flow if applicable
- List of related files

## Constraints
- DO NOT suggest implementations
- DO NOT make changes to code
- Focus only on understanding the current state
- Reference specific line numbers for all claims
```

---

## Sub-Agent Types for Research

### codebase-locator
**Purpose:** Find all files related to a ticket/task
**Output:** List of relevant file paths

### codebase-analyzer  
**Purpose:** Understand how the current implementation works
**Output:** Explanation of code flow with line references

### codebase-pattern-finder
**Purpose:** Find similar features we can model after
**Output:** Examples of existing patterns to follow

### thoughts-locator
**Purpose:** Find any existing thoughts/research documents
**Output:** Links to related documentation

### linear-ticket-reader
**Purpose:** Get full details from Linear tickets
**Output:** Complete ticket context

---

## Example: Research for Icon Bug

**Task:** Bash tool uses wrong icon in message stream

**Research Output:**

```markdown
# Research: Bash Icon Not Showing in Message Stream

## Summary
The Bash tool icon displays correctly in the modal view but shows a generic 
wrench in the message stream. This is because the `eventToDisplayObject` 
function has a different icon mapping than `getToolIcon`.

## Component Usage Flow

### Modal Path (Working)
1. `ToolResultModal.tsx:136` calls `getToolIcon(toolCall?.toolName)`
2. `getToolIcon()` returns `<Terminal />` for Bash
3. Icon displays correctly

### Message Stream Path (Broken)
1. `ConversationContent.tsx:103` calls `eventToDisplayObject(event)`
2. Default `<Wrench />` assigned at line 76
3. No Bash-specific override in lines 189-228
4. Generic wrench icon displays

## Root Cause
`eventToDisplayObject.tsx` has a switch statement for tool-specific icons 
(lines 189-228) but doesn't include a case for "Bash" tool type.

## Code References
- `eventToDisplayObject.tsx:76` - Default wrench assignment
- `eventToDisplayObject.tsx:189-228` - Tool-specific overrides (missing Bash)
- `getToolIcon.tsx:45-67` - Has correct Bash→Terminal mapping

## Pattern to Follow
The fix should add a case to the switch statement at line 189 matching 
the pattern used for other tools like "Read" and "Write".
```

---

## Key Principles

### 1. Research Before Asking
Don't ask the user questions you could answer by reading the code.

### 2. Verify Everything
If the user corrects you, verify the correction in the code.

### 3. Be Specific
"The icon logic" ❌
"`getToolIcon()` in `ToolResultModal.tsx:136`" ✅

### 4. Stay Objective
Research describes what IS, not what SHOULD BE.

### 5. Compress Aggressively
The research doc should be the shortest possible complete summary.
