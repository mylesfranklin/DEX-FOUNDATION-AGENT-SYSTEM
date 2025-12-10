# Practical Prompts and Templates

Ready-to-use prompts for the Research → Plan → Implement workflow.

---

## Research Prompts

### Generic Codebase Research
```markdown
# Research: [Topic]

## Objective
Understand how [SYSTEM/FEATURE] works in this codebase.

## Instructions
1. Find all files related to [TOPIC]
2. Trace the code flow from [ENTRY POINT] to [END POINT]
3. Document key functions and their responsibilities
4. Identify patterns and conventions used
5. Note any edge cases or gotchas

## Output Requirements
Create a research document with:
- Summary of findings (2-3 paragraphs max)
- Detailed code references with exact line numbers
- Data/control flow if applicable
- List of related files

## Constraints
- DO NOT suggest implementations
- DO NOT make changes to code
- Focus only on understanding the current state
- Reference specific line numbers for all claims
```

---

## Planning Prompts

### Feature Implementation Plan
```markdown
# Create Implementation Plan

## Feature
[Feature description]

## Instructions
Create a detailed implementation plan with:

1. **Desired End State** - What success looks like
2. **What We're NOT Doing** - Explicit scope limits
3. **Implementation Approach** - High-level strategy
4. **Phases** - Each with:
   - Overview
   - Changes Required (with file:line and code snippets)
   - Success Criteria (automated and manual verification)

## Requirements
- Include actual code snippets for changes
- Specify exact files and line numbers
- Define automated tests to run after each phase
- Define manual verification steps
```

---

## Implementation Prompts

### Execute Plan
```markdown
# Implement Plan

## Plan Location
[path to plan.md]

## Instructions
1. Read the plan completely before starting
2. Execute phase by phase
3. After each phase:
   - Run automated verification
   - Pause for manual verification if specified
   - Update checkboxes in plan
4. If something doesn't match the plan, stop and communicate

## Phase Completion Format
"Phase [N] Complete

Automated checks:
- ✅ [check 1]
- ✅ [check 2]

Ready for manual verification:
- [ ] [manual check 1]
- [ ] [manual check 2]

Continue to Phase [N+1]?"
```

---

## Compaction Prompts

### End of Session Compaction
```markdown
# Session Compaction

Write everything to progress.md:

- The approach we're taking
- Steps we've done so far  
- Current status
- The current issue/blocker we're working on
- Key file references

This should allow a fresh context to pick up immediately.
```

---

## Slash Command Templates

These can be added to `.claude/commands/` directory:

### /research
```markdown
# /research $TOPIC

Research the specified topic in this codebase.

## Instructions
1. Find all related files
2. Understand the implementation
3. Document findings with file:line references
4. Save to thoughts/shared/research/$TOPIC.md

## Output
A compressed research document ready for planning.
```

### /plan
```markdown
# /plan $TICKET_PATH

Create an implementation plan from a ticket.

## Instructions
1. Read the ticket at $TICKET_PATH
2. Spawn research tasks to gather context
3. Present findings and design options
4. After alignment, write detailed plan
5. Save to thoughts/shared/plans/[feature].md

## Requirements
- Include code snippets
- Include verification criteria
- Include "What We're NOT Doing"
```

### /implement
```markdown
# /implement $PLAN_PATH

Execute an approved implementation plan.

## Instructions
1. Read the plan at $PLAN_PATH
2. Execute phase by phase
3. Run verification after each phase
4. Pause for manual checks as specified
5. Update checkboxes as you complete items
6. Compact progress between phases
```

### /compact
```markdown
# /compact

Compact current session progress.

## Instructions
1. Summarize what we've accomplished
2. Note current state and next steps
3. List key file references
4. Save to appropriate progress file
5. Confirm compaction is complete
```

---

## Usage Tips

1. **Copy and customize** - These are starting points, adapt to your codebase
2. **Be specific** - Replace variables with concrete details
3. **Iterate** - Refine prompts based on what works for your workflow
4. **Save good ones** - Add successful prompts to your `.claude/commands/`
5. **Share with team** - Good prompts benefit everyone
