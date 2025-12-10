# The Implementation Phase

## Purpose

The Implementation phase is where the plan becomes code. With a good research document and detailed plan, this becomes the **least exciting part** of the process—which is exactly the point.

### Goals
- Execute the plan phase by phase
- Verify each phase before moving on
- Compact progress between phases
- Maintain human oversight at key points

---

## Implementation Workflow

### Step 1: Load the Plan

```
/implement_plan thoughts/shared/plans/feature-x.md
```

If no plan path provided, ask for one.

### Step 2: Execute Phase by Phase

For each phase:
1. Read the phase requirements
2. Make the specified changes
3. Run automated verification
4. Pause for manual verification (if specified)
5. Update checkboxes in the plan
6. Compact before next phase

### Step 3: Handle Discrepancies

Reality doesn't always match the plan. When you find discrepancies:

```markdown
## Issue in Phase [N]:

**Expected:** [what the plan says]
**Found:** [actual situation]
**Why this matters:** [explanation]

How should I proceed?
```

Don't silently deviate from the plan. Communicate clearly.

### Step 4: Pause for Human Verification

After completing automated verification for a phase:

```markdown
## Phase [N] Complete - Ready for Manual Verification

Automated verification passed:
- [List automated checks that passed]

Please perform the manual verification steps listed in the plan:
- [List manual verification items from the plan]

Let me know when manual testing is complete so I can proceed to Phase [N+1].
```

Wait for human confirmation before proceeding.

---

## Implementation Prompt Template

```markdown
# Implement Plan

You are tasked with implementing an approved technical plan from 
thoughts/shared/plans/. These plans contain phases with specific 
changes and success criteria.

## Guidelines

**If no plan path provided, ask for one.** Plans are carefully 
designed, but reality can be messy.

Your job is to:
1. Follow the plan's intent while adapting to what you find
2. Implement each phase fully before moving to the next
3. Verify your work makes sense in the broader codebase context
4. Update checkboxes in the plan as you complete sections
5. When things don't match the plan exactly, think about why 
   and communicate clearly

The plan is your guide, but your judgment matters too.

## Phase Completion Protocol

After completing all automated verification for a phase, pause and 
inform the human that the phase is ready for manual testing:

"Phase [N] Complete - Ready for Manual Verification

Automated verification passed:
- [List automated checks that passed]

Please perform the manual verification steps listed in the plan:
- [List manual verification items from the plan]

Let me know when manual testing is complete so I can proceed to 
Phase [N+1]."

## Important Notes

- If instructed to execute multiple phases consecutively, skip the 
  pause until the last phase
- Otherwise, assume you are just doing one phase
- Do not check off items in the manual testing steps until confirmed 
  by the user
- Use sub-tasks sparingly - mainly for targeted debugging or exploring 
  unfamiliar territory
- Remember: You're implementing a solution, not just checking boxes
```

---

## Compaction During Implementation

### When to Compact
- After each phase is complete
- When context is filling up (~40%+)
- Before switching to a different area of the codebase
- When hitting unexpected complexity

### What to Include in Compaction
```markdown
## Implementation Progress: [Feature Name]

### Completed Phases
- [x] Phase 1: [Name] - [Brief summary of what was done]
- [x] Phase 2: [Name] - [Brief summary of what was done]

### Current Phase
- [ ] Phase 3: [Name]
  - Currently working on: [specific task]
  - Blocked by: [if applicable]

### Key Decisions Made
- [Decision 1]: [rationale]
- [Decision 2]: [rationale]

### Files Modified
- `path/to/file1.ts` - [what changed]
- `path/to/file2.go` - [what changed]

### Open Issues
- [Issue 1]: [status]

### Next Steps
1. [Immediate next action]
2. [Following action]
```

### Compaction Command
```
"Compact our current progress to the plan file. Include:
- What phases we've completed
- What we're currently working on
- Any decisions or issues encountered
- Files we've modified"
```

---

## Git Worktrees Note

> "If you've been hearing a lot about git worktrees, this is the only step 
> that needs to be done in a worktree. We tend to do everything else on main."

Research and planning can happen on main. Only implementation needs an isolated branch/worktree to avoid polluting the main codebase with work-in-progress.

---

## Handling Plan Deviations

### Minor Deviations
Small differences between plan and reality (different line numbers, slightly different function signatures):
- Adapt silently if the intent is clear
- Make a note in the phase completion summary

### Moderate Deviations
The general approach works but details need adjustment:
- Implement the spirit of the plan
- Document what you changed and why
- Ask for confirmation if unsure

### Major Deviations
The plan's approach won't work:
```markdown
## Implementation Blocker

I've discovered that the plan's approach for Phase [N] won't work because:

[Explanation of the issue]

**Options:**
1. [Alternative approach 1]
2. [Alternative approach 2]
3. [Revise the plan]

Which direction should we take?
```

**Do not** continue implementing a plan that won't work. Stop and communicate.

---

## Verification Patterns

### Automated Verification Commands
```bash
# TypeScript/JavaScript
npm run typecheck
npm run lint
npm run test

# Go
go build ./...
go test ./...
golangci-lint run

# Python
python -m pytest
python -m mypy .
python -m ruff check .

# General
make check
make test
make lint
```

### Manual Verification Checklist
- [ ] Feature works as described in the plan
- [ ] No regressions in related functionality
- [ ] Error handling works correctly
- [ ] Edge cases handled
- [ ] UI renders correctly (if applicable)
- [ ] Performance is acceptable

---

## Sub-Agents During Implementation

Use sub-agents sparingly during implementation. They're mainly useful for:

### Targeted Debugging
When you hit an unexpected error:
```
Task("Debug why the database migration is failing")
```

### Exploring Unfamiliar Territory
When you need to understand a pattern you haven't seen:
```
Task("Find how other components handle this type of validation")
```

### NOT for:
- General implementation (do it yourself)
- Running tests (do it yourself)
- Making changes (do it yourself)

The goal is focused implementation, not delegation.

---

## Example Implementation Session

```
Human: /implement_plan thoughts/shared/plans/add-directory-support.md

Claude: I'll implement the plan for adding multi-directory support.

Reading the plan... The plan has 3 phases:
1. Backend Infrastructure (database + models)
2. API Layer (endpoints + validation)
3. Desktop App UI (component + integration)

Starting with Phase 1: Backend Infrastructure

[Makes database schema changes]
[Updates Session struct]
[Runs automated verification]

## Phase 1 Complete - Ready for Manual Verification

Automated verification passed:
- ✅ `go build ./...` - No compilation errors
- ✅ `go test ./...` - All tests pass
- ✅ Database migration applies successfully

Please verify:
- [ ] Can create session with additional directories via direct DB
- [ ] Session retrieval includes new field

Let me know when ready to proceed to Phase 2.
```
