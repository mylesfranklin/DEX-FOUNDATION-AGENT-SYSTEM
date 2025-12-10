# Context Engineering Strategies

## The Context is Everything

Every decision the LLM makes is based solely on what's in its context window. There are no hidden memories, no persistent state, no learning from past sessions.

```
┌─────────────────────────────────────────────────────────────┐
│                    CURRENT CONTEXT                          │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  System Instructions                                   │  │
│  │  CLAUDE.md                                            │  │
│  │  Claude Builtin Tools                                 │  │
│  │  MCP Tools                                            │  │
│  │  User message                                         │  │
│  │  Read()                                               │  │
│  │  Search()                                             │  │
│  │  Write()                       ┌─────────────────────┐│  │
│  │  Assistant Message             │  LLM: Determine     ││  │
│  │  User message ──────────────▶  │  Next Step          │├─▶ Right: Edit()
│  │  Read()                        └─────────────────────┘│   Wrong: Write()
│  │  ...                                                  │  │
│  │  Write()                                              │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Optimizing the Context Window

We optimize for four dimensions:

### 1. Correctness
- Information in context must be accurate
- Incorrect information leads to incorrect outputs
- Verify research outputs before proceeding

### 2. Completeness
- Missing information leads to hallucination
- Agent will "fill in blanks" with plausible but wrong info
- Pre-fetch context you might need

### 3. Size
- Smaller is better (within completeness constraints)
- Stay in the Smart Zone (<40% utilization)
- Compress aggressively

### 4. Trajectory
- Pattern of the conversation matters
- Error-correction patterns breed more errors
- Fresh start is often better than correction

---

## Strategy 1: Progressive Context Disclosure

Instead of loading all documentation upfront, reveal context progressively as the agent works through the codebase.

### Repository-Level Structure
```
project/
├── CLAUDE.md              # Root-level context
├── src/
│   ├── CLAUDE.md          # src-specific context
│   └── components/
│       └── CLAUDE.md      # Component-specific context
└── api/
    └── CLAUDE.md          # API-specific context
```

### Why This Works
- Agent pulls in root context first
- As it navigates to specific areas, additional context loads
- Keeps total context manageable
- Each CLAUDE.md is specialized for its directory

### The Lie Problem
As codebases grow, documentation gets out of date. The hierarchy from most to least truthful:

```
TRUTH LEVEL
    ▲
    │  The actual code, function names, comments
    │      ▲ (Most truthful)
    │
    │  CLAUDE.md / documentation
    │      ▼ (Can drift from reality)
    │
    └──────────────────────────────────────▶
```

**Solution:** Generate context on-demand from the code itself.

---

## Strategy 2: On-Demand Compressed Context

Instead of maintaining static documentation, generate focused research documents for each task.

### Process
1. User provides task + steering (which parts of codebase are relevant)
2. Research prompt spawns sub-agents to explore vertically
3. Sub-agents return findings
4. Findings are compressed into a research document
5. Research document is based on actual code (ground truth)

### Example
```
User: "We need to add a feature for SCM providers and Jira integration"

     │
     ▼
┌─────────────────────────────────────────┐
│          STEERING                        │
│  "Focus on: SCM providers, Jira, Linear" │
└─────────────────────────────────────────┘
     │
     ▼
┌─────────┬─────────┬─────────┐
│ SCM     │ Jira    │ Linear  │
│ Agent   │ Agent   │ Agent   │
└────┬────┴────┬────┴────┬────┘
     │         │         │
     ▼         ▼         ▼
┌─────────────────────────────────────────┐
│        RESEARCH DOCUMENT                 │
│  (Snapshot of actual code truth)         │
│  - Relevant files with line numbers      │
│  - Current implementations               │
│  - Patterns to follow                    │
└─────────────────────────────────────────┘
```

---

## Strategy 3: Intentional Compaction

Deliberately compress accumulated context into a markdown file at strategic points.

### When to Compact
- Before context window fills up
- After completing a logical phase
- When switching focus areas
- Before taking a break

### Compaction Prompt
```
"Write everything we did so far to progress.md, ensure to note:
- The approach we're taking
- The steps we've done so far
- The current failure we're working on"
```

### Before/After Compaction

**BEFORE:**
```
┌────────────────────────────────┐
│ System Instructions            │
│ CLAUDE.md                      │
│ Claude Builtin Tools           │
│ MCP Tools                      │
│ User message                   │
│ Read()                         │
│ Search()                       │
│ Write()                        │
│ Assistant Message              │
│ User message: Great, now ABC   │
│ Read()                         │
│ ...                            │
│ Write()                        │
│ Assistant Message              │
│ [Summarize to progress.md] ──────▶ progress.md
└────────────────────────────────┘
```

**AFTER (New Context):**
```
┌────────────────────────────────┐
│ System Instructions            │
│ CLAUDE.md                      │
│ Claude Builtin Tools           │
│ MCP Tools                      │
│ User message: Read progress.md │
│ Read() ◀────────────────────────── progress.md
│ Write()                        │
│ Write()                        │
│ Write()                        │
│ ...                            │
└────────────────────────────────┘
```

The new context starts small, picks up exactly where we left off, and has full capacity for more work.

---

## Strategy 4: Context-Controlled Sub-Agents

Sub-agents are NOT for anthropomorphizing roles. They are for controlling context.

### Wrong Way (Role-Based)
```
❌ "frontend sub-agent"
❌ "backend sub-agent"  
❌ "QA sub-agent"
❌ "data scientist sub-agent"
```

### Right Way (Task-Based)
```
✅ "Find where X is handled"
✅ "Research how authentication flow works"
✅ "Locate all files related to the session model"
```

### How Sub-Agents Control Context

**Parent Agent:**
```
┌────────────────────────────────┐
│ System Instructions            │
│ CLAUDE.md                      │
│ Claude Builtin Tools           │
│ MCP Tools                      │
│ User message                   │
│ Task("Find where XYZ handled") │──▶ Spawns sub-agent
│       │                        │
│       │  "the file is in       │
│       │   src/main/..."        │
│       ◀────────────────────────│◀── Receives summary
│ Read()                         │
│ Edit()                         │
│ Write()                        │
└────────────────────────────────┘
```

**Sub-Agent (Isolated Context):**
```
┌────────────────────────────────┐
│ System Instructions            │
│ CLAUDE.md                      │
│ Claude Builtin Tools           │
│ MCP Tools                      │
│ User message: Find XYZ         │
│ Read()                         │  ← Does all the
│ Read()                         │    expensive searching
│ Read()                         │    in isolated context
│ Search()                       │
│ List()                         │
│ Assistant Message: ────────────│──▶ Returns ONLY the answer
│  "the file is in src/main/..." │
└────────────────────────────────┘
       (context is discarded)
```

The parent agent gets just the answer, not all the exploration noise.

---

## Strategy 5: Frequent Intentional Compaction (FIC)

The complete workflow built around constant context management:

```
┌─────────────────────────────────────────────────────────────┐
│                FREQUENT INTENTIONAL COMPACTION               │
│                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │ RESEARCH │───▶│   PLAN   │───▶│IMPLEMENT │              │
│  │          │    │          │    │ Phase 1  │              │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘              │
│       │               │               │                     │
│       ▼               ▼               ▼                     │
│  research.md      plan.md        compact()                  │
│                                       │                     │
│                                       ▼                     │
│                               ┌──────────┐                  │
│                               │IMPLEMENT │                  │
│                               │ Phase 2  │                  │
│                               └────┬─────┘                  │
│                                    │                        │
│                                    ▼                        │
│                               compact()                     │
│                                    │                        │
│                                   ...                       │
└─────────────────────────────────────────────────────────────┘
```

Each phase produces compressed artifacts. Each implementation phase compacts before moving to the next. Context stays small throughout.

---

## Anti-Patterns to Avoid

### 1. MCP Tool Overload
If your MCP tools dump large JSON blobs and UUIDs into context, you're doing all your work in the Dumb Zone.

### 2. Correction Loops
```
❌ Tell agent it's wrong → correct → wrong again → correct...
✅ Start fresh context with lessons learned
```

### 3. Kitchen-Sink Context
```
❌ Load entire codebase documentation upfront
✅ Load only what's relevant, progressively
```

### 4. Ignoring the Dumb Zone
```
❌ Keep working as context fills up
✅ Compact proactively before hitting ~40%
```

### 5. Role-Based Sub-Agents
```
❌ "You are the frontend specialist agent..."
✅ "Find where the session modal handles directories"
```
