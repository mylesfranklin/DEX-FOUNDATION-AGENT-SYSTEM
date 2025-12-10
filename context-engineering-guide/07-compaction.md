# Intentional Compaction Strategies

## What is Compaction?

Compaction is the deliberate process of condensing accumulated context into a compressed, information-dense summary. It allows you to:

1. **Reset context utilization** - Get back to the Smart Zone
2. **Preserve essential information** - Nothing important is lost
3. **Continue work seamlessly** - New context picks up where old left off
4. **Maintain quality** - Stay out of the Dumb Zone

---

## When to Compact

### Proactive Compaction (Recommended)
- **Before reaching 40% context** - Stay in Smart Zone
- **After completing a logical phase** - Natural breakpoint
- **Before switching focus areas** - Clean slate for new work
- **At end of work session** - Enable seamless pickup later

### Reactive Compaction (Emergency)
- **Model performance degrading** - "You're absolutely right" appearing
- **Repetitive mistakes** - Pattern suggests Dumb Zone
- **Context nearly full** - Last resort

---

## What to Include in Compaction

### Essential Elements
1. **Current Task** - What we're working on
2. **Approach** - The strategy being used
3. **Key Files** - Exact paths and line numbers
4. **Completed Steps** - What's done
5. **Current Focus** - What we're doing now
6. **Blockers** - Current issues if any

### Good Compaction Example
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

## Blockers
Need to determine how to validate paths before passing to Claude
```

### Bad Compaction Example (Too Verbose)
```markdown
## Everything That Happened

First, I started by reading the issue which was about adding support
for multiple directories. Then I looked at the database schema and 
found it in sqlite.go. I tried a few different approaches to the 
migration. The first approach didn't work because... [continues for
500 more words]

## Files I Looked At
- main.go (just to understand entry point)
- daemon.go (saw some interesting things here)
- session.go (this is where the main logic is)
- store.go (database stuff)
- [20 more files listed]
```

This doesn't compress—it just retells. Compress means **summarize to essentials only**.

---

## Compaction Prompts

### End of Phase Compaction
```
"Compact our progress to progress.md before moving to the next phase. Include:
- What phase we completed
- Key files modified
- Decisions made
- Ready state for next phase"
```

### End of Session Compaction
```
"Write everything we did so far to progress.md, ensure to note:
- The approach we're taking
- The steps we've done so far
- The current failure we're working on"
```

### Research Compaction
```
"Compress our research findings to research.md. Include:
- Summary of findings
- Key file references with line numbers
- Patterns identified
- Open questions"
```

### Emergency Compaction
```
"Our context is getting full. Compact immediately to progress.md:
- Current state
- Next step
- Essential context only"
```

---

## Frequent Intentional Compaction (FIC)

The complete workflow built around constant compaction:

```
┌───────────────────────────────────────────────────────────────────┐
│              FREQUENT INTENTIONAL COMPACTION WORKFLOW              │
│                                                                    │
│  Research ──▶ research.md ──▶ Plan ──▶ plan.md                    │
│                                            │                       │
│                                            ▼                       │
│                              ┌─────────────────────┐              │
│                              │    Implement        │              │
│                              │    Phase 1          │              │
│                              └──────────┬──────────┘              │
│                                         │                          │
│                                         ▼                          │
│                              compact to plan.md                    │
│                                         │                          │
│                                         ▼                          │
│                              ┌─────────────────────┐              │
│                              │    Implement        │              │
│                              │    Phase 2          │              │
│                              └──────────┬──────────┘              │
│                                         │                          │
│                                         ▼                          │
│                              compact to plan.md                    │
│                                         │                          │
│                                        ...                         │
│                                         │                          │
│                                         ▼                          │
│                                      Done                          │
└───────────────────────────────────────────────────────────────────┘
```

### Key Points
- Research produces a compressed document
- Plan produces a compressed document
- Each implementation phase compacts before the next
- Context stays small throughout the entire workflow

---

## Compaction Checklist

Before creating a new context, verify your compaction includes:

- [ ] What task we're working on
- [ ] What approach we're taking
- [ ] What files are relevant (with line numbers)
- [ ] What's been completed
- [ ] What's next
- [ ] Any blockers or open questions
- [ ] Key decisions made

If you have all of these, a fresh context can pick up immediately.
