# The Planning Phase

## Purpose

The Planning phase transforms research into a **detailed implementation blueprint**. The plan should be so specific that even a "dumb model" wouldn't screw it up.

### Goals
- Outline exact steps to take
- Include filenames and line numbers
- Include actual code snippets
- Define explicit testing/verification for each change

---

## Plan Output: What Good Looks Like

A good plan includes **actual code snippets** of what's going to change:

```markdown
## Desired End State

Users can specify additional directories for Claude Code to access:
- Desktop App: Select multiple directories via UI with fuzzy search
- CLI: Pass directories via `--add-dir` flag
- Sessions inherit additional directories when continued
- Directories are validated at creation time
- Additional directories displayed in session table

## What We're NOT Doing

- Supporting relative paths in Desktop App (only fuzzy search)
- Implementing directory quotas beyond Claude's limits
- Auto-expanding directory lists (each must be explicitly added)
- Modifying existing working directory behavior
- Supporting wildcards or glob patterns

## Implementation Approach

Start with backend infrastructure to support the data model, fix the 
existing array passing bug, then build UI components for the Desktop 
App, and finally add CLI support. This order ensures each layer has 
the necessary foundation.

## Phase 1: Backend Infrastructure

### Success Criteria

#### Automated Verification:
- [ ] TypeScript compilation: `make -C humanlayer-wui check`
- [ ] Linting passes: `make -C humanlayer-wui lint`
- [ ] Tests pass: `make -C humanlayer-wui test`

#### Manual Verification:
- [ ] Can add multiple directories via UI
- [ ] Directory validation works (non-existent paths rejected)

### Changes Required

#### 1. Database Schema
**File:** `hld/store/sqlite.go` **Changes:** Add migration for additional_directories column

```go
// After line 289 in applyMigrations function
alterations = []struct {
    column string
    sql    string
}{
    {"additional_directories", "ALTER TABLE sessions ADD COLUMN additional_directories TEXT"},
}

for _, alt := range alterations {
    var exists int
    err = s.db.QueryRow(`
        SELECT COUNT(*) FROM pragma_table_info('sessions') WHERE name = ?
    `, alt.column).Scan(&exists)
    
    if exists == 0 {
        if _, err := s.db.Exec(alt.sql); err != nil {
            return fmt.Errorf("failed to add %s column: %w", alt.column, err)
        }
    }
}
```

#### 2. Session Struct
**File:** `hld/store/store.go` **Changes:** Add field after line 78

```go
type Session struct {
    // ... existing fields ...
    AdditionalDirectories []string `json:"additional_directories,omitempty"`
}
```

## Phase 3: Desktop App UI Implementation

### Overview
Create multi-directory selector component for the Desktop App session launcher modal.

### Changes Required

#### 1. Multi-Directory Input Component
**File:** `humanlayer-wui/src/components/MultiDirectoryInput.tsx` (new file) **Changes:** Create new component

```tsx
import React, { useState } from 'react'
import { XIcon } from 'lucide-react'
import { Badge } from '@/components/ui/badge'
import { FuzzySearchInput } from '@/components/FuzzySearchInput'
import { cn } from '@/lib/utils'
import { validatePath } from '@/lib/daemon/utils'

interface MultiDirectoryInputProps {
    directories: string[]
}

// ... implementation
```
```

Notice:
- Clear end state definition
- Explicit "NOT doing" section (prevents scope creep)
- Phased implementation approach
- Automated AND manual verification criteria
- Actual code snippets with specific line numbers

---

## Plan Structure Template

```markdown
## Desired End State

[A specification of the desired end state after this plan is complete, 
and how to verify it]

### Key Discoveries:
- [Important finding with file:line reference]
- [Pattern to follow]
- [Constraint to work within]

## What We're NOT Doing

[Explicitly list out-of-scope items to prevent scope creep]

## Implementation Approach

[High-level strategy and reasoning]

## Phase 1: [Descriptive Name]

### Overview
[What this phase accomplishes]

### Changes Required:

#### 1. [Component Name]
**File:** `path/to/file.ext` **Changes:** [Description]

```language
// Actual code snippet showing the change
```

### Success Criteria:

#### Automated Verification:
- [ ] [Test command]: `make test`
- [ ] [Lint command]: `npm run lint`
- [ ] [Type check]: `tsc --noEmit`

#### Manual Verification:
- [ ] [Manual test scenario 1]
- [ ] [Manual test scenario 2]

## Phase 2: [Descriptive Name]
...
```

---

## Planning Workflow

### Step 1: Read Research & Ticket

If a plan path is provided:
```
/create_plan thoughts/shared/plans/feature-x.md
```

If no path, ask for one. Plans live in `thoughts/shared/plans/`.

### Step 2: Spawn Research Tasks

Before asking the user any questions, gather context in parallel:

```python
# Spawn these tasks concurrently:
tasks = [
    Task("Research database schema", db_research_prompt),
    Task("Find API patterns", api_research_prompt),
    Task("Investigate UI components", ui_research_prompt),
    Task("Check test patterns", test_research_prompt)
]
```

Use specialized agents:
- `codebase-locator` - Find all files related to the ticket/task
- `codebase-analyzer` - Understand implementation details
- `codebase-pattern-finder` - Find similar features to model after

### Step 3: Present Findings and Options

Wait for ALL sub-tasks to complete, then:

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

### Step 4: Develop Plan Structure

Once aligned on approach:

```markdown
Here's my proposed plan structure:

## Overview
[1-2 sentence summary]

## Implementation Phases:
1. [Phase name] - [what it accomplishes]
2. [Phase name] - [what it accomplishes]
3. [Phase name] - [what it accomplishes]

Does this phasing make sense? Should I adjust the order or granularity?
```

### Step 5: Write the Full Plan

After user confirms structure, write the complete plan with:
- Actual code snippets
- Specific file paths and line numbers
- Clear success criteria for each phase

---

## Code Snippets in Plans

### Why Include Code?
- Removes ambiguity
- Reviewer can see exactly what will change
- Model can execute more reliably
- Easier to catch issues before implementation

### How Much Code?
Include enough to show:
- What's being added/changed
- Where in the file (line numbers)
- How it fits with surrounding code

### Example: Too Vague
```markdown
#### Database Migration
Add a new column to the sessions table for additional directories.
```

### Example: Good Detail
```markdown
#### Database Migration
**File:** `hld/store/sqlite.go` **Changes:** Add migration for additional_directories column

```go
// After line 289 in applyMigrations function
alterations = []struct {
    column string
    sql    string
}{
    {"additional_directories", "ALTER TABLE sessions ADD COLUMN additional_directories TEXT"},
}
```
```

---

## Success Criteria

Every phase MUST have explicit success criteria.

### Automated Verification
Commands that can be run to verify the phase is complete:
- `make test` - Run test suite
- `npm run lint` - Check linting
- `tsc --noEmit` - Type check
- `go build ./...` - Compilation check
- `curl localhost:8080/api/endpoint` - API smoke test

### Manual Verification
Human-checkable items:
- [ ] New feature appears correctly in the UI
- [ ] Performance is acceptable with 1000+ items
- [ ] Error messages are user-friendly
- [ ] Feature works correctly on mobile devices

---

## Mental Alignment: Why Plans Matter

> "Code review is about mental alignment - keeping members of the team on the same page about how the codebase is changing and why."

As AI ships more code:
- Humans can't review 2000+ lines of code daily
- Humans CAN review 200 lines of a well-written plan
- Plans provide the "why" that pure code review misses
- Plans catch architectural issues before they become code

### Mitchell Hashimoto's Practice
He includes his Amp threads on pull requests so reviewers can see:
- The exact steps taken
- The prompts used
- That tests passed

This takes the reviewer on a journey that GitHub diffs alone cannot.

---

## The Leverage Equation

```
As plans get longer:
  - Reliability of execution goes UP
  - Readability for humans goes DOWN

Find the sweet spot for your team.
```

### Guidelines
- Simple feature: 1-2 phases, basic code snippets
- Medium feature: 3-4 phases, detailed code snippets
- Complex feature: 5+ phases, comprehensive code, possibly split into multiple plans

---

## Key Principles

### 1. Plans Must Be Readable
If you can't understand the plan quickly, neither can the model.

### 2. Plans Must Be Verifiable
Every claim should be checkable against the code or tests.

### 3. Plans Must Be Scoped
Explicit "What We're NOT Doing" prevents scope creep.

### 4. Plans Must Be Phased
Breaking into phases enables compaction between them.

### 5. Plans Must Have Code
Without code snippets, you're hoping the model guesses correctly.
