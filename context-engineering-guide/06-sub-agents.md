# Managing Context with Sub-Agents

## The Core Principle

> **Sub-agents are NOT for anthropomorphizing roles. They are for controlling context.**

Sub-agents allow you to fork off a new context window to do work (usually exploration/research) and return only the relevant results to the parent context.

---

## How Sub-Agents Work

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PARENT AGENT                                     │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ System Instructions                                                │  │
│  │ CLAUDE.md                                                          │  │
│  │ Claude Builtin Tools                                               │  │
│  │ MCP Tools                                                          │  │
│  │ User message                                                       │  │
│  │ Task("Find where XYZ is handled") ──────────────────────────────┐ │  │
│  │      │                                                           │ │  │
│  │      │  "the file is in src/main/..."                           │ │  │
│  │      ◀──────────────────────────────────────────────────────────┘ │  │
│  │ Read()  ◀── Only reads the ONE file identified                    │  │
│  │ Edit()                                                             │  │
│  │ Write()                                                            │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Spawns
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         SUB-AGENT (Isolated Context)                     │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │ System Instructions                                                │  │
│  │ CLAUDE.md                                                          │  │
│  │ Claude Builtin Tools                                               │  │
│  │ MCP Tools                                                          │  │
│  │ User message: "Find where XYZ is handled"                         │  │
│  │ Read()    ◀── Reads many files                                    │  │
│  │ Read()    ◀── exploring the                                       │  │
│  │ Read()    ◀── codebase                                            │  │
│  │ Search()                                                           │  │
│  │ List()                                                             │  │
│  │ Assistant Message: "the file is in src/main/..." ─────────────────┼──▶ Returns ONLY answer
│  └───────────────────────────────────────────────────────────────────┘  │
│                        (context is discarded)                            │
└─────────────────────────────────────────────────────────────────────────┘
```

**Key insight:** The sub-agent can consume massive amounts of context exploring the codebase. The parent only receives a succinct summary. All that exploration noise is discarded.

---

## Right vs. Wrong Use of Sub-Agents

### ❌ Wrong: Role-Based Sub-Agents
```
"frontend sub-agent"
"backend sub-agent"
"QA sub-agent"
"data scientist sub-agent"
```

This doesn't control context—it just adds ceremony.

### ✅ Right: Task-Based Sub-Agents
```
"Find where session creation is handled"
"Research how the authentication flow works"
"Locate all files related to the icon system"
"Find the database migration patterns used"
```

These are focused tasks that return specific, actionable information.

---

## When to Use Sub-Agents

### Good Use Cases

1. **Codebase Exploration**
   - Finding where a feature is implemented
   - Tracing data flow through the system
   - Locating relevant files for a task

2. **Pattern Discovery**
   - Finding existing examples to follow
   - Understanding conventions used in the codebase
   - Locating test patterns

3. **Research Gathering**
   - Investigating how a subsystem works
   - Finding documentation or comments
   - Building context for planning

### Bad Use Cases

1. **Implementation** - Do it yourself in main context
2. **Running Tests** - Do it yourself
3. **Making Changes** - Do it yourself
4. **Verification** - Do it yourself

---

## Sub-Agent Types (HumanLayer Patterns)

### codebase-locator
**Purpose:** Find all files related to a specific topic
**Input:** Topic or feature name
**Output:** List of relevant file paths

```
Task("codebase-locator", "Find all files related to session management")
```

### codebase-analyzer
**Purpose:** Understand how a specific implementation works
**Input:** Feature or code area to analyze
**Output:** Explanation of code flow with line references

```
Task("codebase-analyzer", "Analyze how the modal system handles tool icons")
```

### codebase-pattern-finder
**Purpose:** Find similar existing features to model new work after
**Input:** Description of what you want to build
**Output:** Examples of existing patterns with file references

```
Task("codebase-pattern-finder", "Find examples of form validation in UI components")
```

### thoughts-locator
**Purpose:** Find existing documentation or research
**Input:** Topic to search for
**Output:** Links to relevant thoughts/research documents

```
Task("thoughts-locator", "Find any existing research about the icon system")
```

### linear-ticket-reader
**Purpose:** Get full details from Linear tickets
**Input:** Ticket ID or reference
**Output:** Complete ticket context

```
Task("linear-ticket-reader", "Get details for ENG-1234")
```

---

## Parallel Sub-Agent Execution

Sub-agents can be spawned in parallel for maximum efficiency:

```python
# Instead of sequential:
result1 = await Task("Research database schema")
result2 = await Task("Find API patterns")
result3 = await Task("Investigate UI components")

# Spawn in parallel:
tasks = [
    Task("Research database schema", db_research_prompt),
    Task("Find API patterns", api_research_prompt),
    Task("Investigate UI components", ui_research_prompt),
    Task("Check test patterns", test_research_prompt)
]
results = await gather(*tasks)
```

### Wait for ALL Sub-Tasks
Always wait for all sub-tasks to complete before proceeding:

```markdown
4. Wait for ALL sub-tasks to complete before proceeding
5. Present findings and design options
```

Partial results lead to incomplete understanding.

---

## Sub-Agent Output Format

### Good Output (Concise, Actionable)
```
"The session creation logic is in hld/daemon/session.go:145-200. 
Key function is CreateSession() which calls validateDirectories() 
at line 167 before persisting to the database."
```

### Bad Output (Too Verbose)
```
"I found many interesting things in the codebase. First, I looked 
at the main.go file which has the entry point. Then I explored the 
daemon package which has several files. The session.go file seems 
to be important. There's also a store package that handles database 
operations. The CreateSession function does validation and then 
saves to the database. There are also tests in session_test.go..."
```

The parent context doesn't need the journey—just the destination.

---

## Context Budget for Sub-Agents

Sub-agents have their own context windows. They can:
- Fill up their context exploring
- Make mistakes and recover
- Go down wrong paths

None of this pollutes the parent context.

```
Parent Context Budget:
├── Task spawn: ~50 tokens
├── Task result: ~100 tokens (compressed finding)
└── Total impact: ~150 tokens

Sub-Agent Context Budget (invisible to parent):
├── Exploration: 10,000+ tokens
├── Wrong paths: 5,000+ tokens
├── Final synthesis: 2,000 tokens
└── Only the result comes back
```

---

## Steering Sub-Agents

Provide clear, focused instructions:

### Too Vague
```
Task("Look at the authentication code")
```

### Better
```
Task("Find where JWT tokens are validated in the auth middleware. 
Return the file path and line numbers of the validation logic.")
```

### Best
```
Task("codebase-analyzer", """
Find the JWT token validation logic:
1. Locate the auth middleware
2. Find the token validation function
3. Return:
   - File path and line numbers
   - What claims are validated
   - How expiration is handled
""")
```

---

## Error Handling in Sub-Agents

Sub-agents might fail or return unhelpful results. Handle gracefully:

```python
result = await Task("Find authentication logic")

if result.status == "failed":
    # Try alternative approach
    result = await Task("Search for 'jwt' in the codebase")

if result.is_inconclusive:
    # Ask for more specific steering
    raise NeedMoreContext("Couldn't find auth logic. Which package should I look in?")
```

---

## Anti-Patterns

### 1. Sub-Agent Chains
```
❌ Task A → Task B → Task C → Task D
```
Each hop loses context. Better to do one focused task.

### 2. Implementation in Sub-Agents
```
❌ Task("Implement the feature")
```
Implementation should happen in the main context where you can verify and iterate.

### 3. Chatty Sub-Agents
```
❌ Sub-agent returns 2000 tokens of findings
```
If the result is that long, the sub-agent should compress it first.

### 4. Unsteered Exploration
```
❌ Task("Explore the codebase")
```
Without focus, sub-agents waste context on irrelevant areas.

---

## Summary

Sub-agents are a powerful tool for **context control**, not for role-playing. Use them to:

1. **Explore** the codebase in isolated context
2. **Research** how things work without polluting parent context
3. **Find** specific information and return only what's relevant
4. **Compress** exploration into actionable insights

Keep sub-agent tasks focused, and always wait for all results before proceeding.
