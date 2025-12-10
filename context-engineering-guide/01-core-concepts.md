# Core Concepts and Terminology

## The Fundamental Insight

> **LLMs are stateless functions.** Every turn of the loop when Claude is picking the next tool—there could be hundreds of right next steps and hundreds of wrong next steps—but the only thing that influences what comes out next is what is in the conversation so far.

This is the foundational principle of context engineering. Unlike traditional software where state persists, LLMs start fresh with every inference. The context window IS the state.

---

## Key Terms

### Context Window
The total amount of text (tokens) an LLM can "see" at once. For Claude, this is roughly 168,000 tokens, with some reserved for output.

### Context Engineering
The practice of deliberately managing what goes into the context window to maximize output quality. This includes:
- Selecting relevant information
- Compressing verbose content
- Removing noise
- Maintaining trajectory

### The Smart Zone vs. The Dumb Zone

```
┌────────────────────────────────────────────────────────┐
│                    CONTEXT WINDOW                       │
│  ┌──────────────────────────────────────────────────┐  │
│  │              SMART ZONE (0-40%)                  │  │
│  │                                                   │  │
│  │  • Best model performance                         │  │
│  │  • Accurate tool selection                        │  │
│  │  • Good reasoning                                 │  │
│  ├──────────────────────────────────────────────────┤  │
│  │            DIMINISHING RETURNS (~40%)            │  │
│  ├──────────────────────────────────────────────────┤  │
│  │              DUMB ZONE (40-100%)                 │  │
│  │                                                   │  │
│  │  • Degraded performance                           │  │
│  │  • Poor reasoning                                 │  │
│  │  • Increased hallucination                        │  │
│  │  • "Lost in the middle" problem                   │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

**Key insight**: "The more you use the context window, the worse outcomes you'll get."

### Compaction
The process of condensing accumulated context into a smaller, information-dense summary. This allows you to:
- Reset context utilization
- Preserve essential information
- Continue work without degradation

### Sub-Agent
A forked context window spawned to perform a specific task (usually research) that returns a condensed result to the parent agent. The sub-agent can consume lots of context exploring, but only returns what's relevant.

### Trajectory
The pattern of conversation history. LLMs are pattern-completion machines, so a conversation full of mistakes and corrections will tend to produce more mistakes.

**Bad trajectory:**
```
Human: Do X
Claude: [does wrong thing]
Human: No, that's wrong
Claude: [does another wrong thing]
Human: Still wrong!
```

The model learns: "In this conversation, I do wrong things."

**Good trajectory:** Start fresh when going off-track.

---

## The Research-Plan-Implement (RPI) Workflow

### Phase 1: Research
**Goal:** Understand how the system works
- Find relevant files
- Trace code flows
- Stay objective
- Output: Compressed research document

### Phase 2: Plan  
**Goal:** Outline exact implementation steps
- Include filenames and line numbers
- Specify code snippets
- Define testing/verification for each change
- Output: Detailed implementation plan

### Phase 3: Implement
**Goal:** Execute the plan phase by phase
- Follow the plan precisely
- Compact progress between phases
- Verify each step
- Output: Working, tested code

---

## Why This Works

### Traditional AI Coding Approach
```
Human: Build feature X
   │
   ▼
[Long conversation with corrections]
   │
   ▼
Context fills up
   │
   ▼
Model enters Dumb Zone
   │
   ▼
Poor outputs, frustration
```

### Context Engineering Approach
```
Human: Research how Y works
   │
   ▼
[Focused research in sub-agent]
   │
   ▼
Compressed research.md (stays in Smart Zone)
   │
   ▼
Human: Create plan for feature X
   │
   ▼
[Plan created from research]
   │
   ▼
Compressed plan.md (stays in Smart Zone)
   │
   ▼
Human: Implement phase 1
   │
   ▼
[Focused implementation]
   │
   ▼
Compact progress → Fresh context for phase 2
```

---

## Mental Models

### "Memento" Model
Like the movie where the protagonist has no memory and must read his own notes to understand his situation—agents need to be "onboarded" with context or they will make things up.

### "Compile Time" Model
Research and planning are like compile-time checks. Catching errors there is far cheaper than at runtime (implementation).

### "Leverage Point" Model
- Bad research = entire project hosed
- Bad plan = 100+ bad lines of code  
- Bad code = 1 bad line of code

Put human review at the highest leverage points.

---

## What Takes Up Context Space?

1. **File reading/searching** - Exploring the codebase
2. **Code understanding** - Analyzing how things work
3. **File editing** - The edits themselves
4. **Test/build output** - Command results
5. **MCP tool responses** - JSON, UUIDs, metadata

The biggest context hogs are exploration activities, which is why sub-agents are so powerful—they do the exploration in isolated context.

---

## The Compaction Principle

### What Goes Into Compaction?
- Exact files and line numbers relevant to the problem
- Current approach being taken
- Steps completed so far
- Current blockers/failures being worked on

### What a Good Compaction Looks Like
```markdown
## Current Task
Adding multi-directory support to session launcher

## Key Files
- humanlayer-wui/src/components/MultiDirectoryInput.tsx:1-45
- hld/store/sqlite.go:289 (applyMigrations function)
- hld/store/store.go:78 (Session struct)

## Approach
1. Add DB migration for additional_directories column
2. Update Session struct with new field
3. Create UI component with fuzzy search
4. Wire up to session creation flow

## Completed
- [x] Database schema migration
- [x] Session struct updated

## Current Focus
Building MultiDirectoryInput component, stuck on path validation
```

This allows a new context window to pick up exactly where the previous one left off.
