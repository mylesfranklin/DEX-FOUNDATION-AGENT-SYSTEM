# Advanced Context Engineering for Claude Code

> A comprehensive guide to solving hard problems in complex codebases using AI coding agents

**Based on Dex Horthy's "No Vibes Allowed" methodology from HumanLayer**

---

## 📋 Table of Contents

| Document | Description |
|----------|-------------|
| [01-core-concepts.md](./context-engineering-guide/01-core-concepts.md) | Foundational concepts and terminology |
| [02-context-engineering.md](./context-engineering-guide/02-context-engineering.md) | Context window optimization strategies |
| [03-research-phase.md](./context-engineering-guide/03-research-phase.md) | The Research phase workflow |
| [04-plan-phase.md](./context-engineering-guide/04-plan-phase.md) | The Planning phase workflow |
| [05-implement-phase.md](./context-engineering-guide/05-implement-phase.md) | The Implementation phase workflow |
| [06-sub-agents.md](./context-engineering-guide/06-sub-agents.md) | Managing context with sub-agents |
| [07-compaction.md](./context-engineering-guide/07-compaction.md) | Intentional compaction strategies |
| [08-12-factor-agents.md](./context-engineering-guide/08-12-factor-agents.md) | The 12 Factor Agents principles |
| [09-practical-prompts.md](./context-engineering-guide/09-practical-prompts.md) | Ready-to-use prompts and templates |
| [10-humanlayer-resources.md](./context-engineering-guide/10-humanlayer-resources.md) | HumanLayer repos and resources |

---

## 🎯 The Problem

A Stanford study surveying 100,000 developers found that most AI-assisted coding results in significant rework and codebase churn. AI doesn't work well for:

- **Complex tasks** requiring deep understanding
- **Brownfield codebases** (existing, legacy code)
- **Large-scale changes** across multiple files

The common responses:
- "Too much slop"
- "Tech debt factory"
- "Maybe someday when models get better"

## 💡 The Solution: Context Engineering

**Context Engineering** is about getting the most out of today's models by managing what goes into the context window. The key insight:

> LLMs are stateless. The only way to get better outputs is to put better tokens in.

This methodology enables:
- ✅ **2-3x throughput** increase
- ✅ **No slop** - high-quality, reviewable code
- ✅ **Mental alignment** - team stays synchronized
- ✅ **Complex problem solving** in brownfield codebases

---

## 🔄 The Core Workflow: Research → Plan → Implement

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  RESEARCH   │───▶│    PLAN     │───▶│  IMPLEMENT  │
│             │    │             │    │             │
│ Understand  │    │ Outline     │    │ Execute     │
│ the system  │    │ exact steps │    │ phase by    │
│             │    │             │    │ phase       │
└─────────────┘    └─────────────┘    └─────────────┘
      │                  │                  │
      ▼                  ▼                  ▼
 research.md         plan.md         working code
```

### Key Principles

1. **Stay in the "Smart Zone"** - Keep context window under ~40% utilization
2. **Frequent Intentional Compaction** - Regularly compress progress into markdown
3. **Use Sub-Agents for Exploration** - Fork context for research, return summaries
4. **Human Review at Leverage Points** - Review plans, not just code

---

## ⚠️ Critical Mindset: Don't Outsource the Thinking

> **AI cannot replace thinking. It can only amplify the thinking you have done, or the lack of thinking you have done.**

This methodology requires active engagement:
- Read and verify research outputs
- Review and approve plans before implementation
- Stay engaged throughout the process

A bad line of research → bad plan → hundreds of bad lines of code.

---

## 📊 The Leverage Hierarchy

```
ERROR IMPACT
     ▲
     │  ┌──────────────────────┐
     │  │     RESEARCH         │  ← One bad line = entire project hosed
     │  │  (highest leverage)  │
     │  └──────────────────────┘
     │  ┌──────────────────────┐
     │  │       PLAN           │  ← One bad line = 100+ bad code lines
     │  │  (medium leverage)   │
     │  └──────────────────────┘
     │  ┌──────────────────────┐
     │  │       CODE           │  ← One bad line = one bad line
     │  │   (low leverage)     │
     │  └──────────────────────┘
     └─────────────────────────────▶ EFFORT
```

**Move human effort to the highest leverage points.**

---

## 🚀 Quick Start

### For Simple Tasks (button color changes)
Just talk to the agent directly. No ceremony needed.

### For Medium Features
1. Create research doc
2. Build implementation plan
3. Execute plan

### For Complex Features (multi-repo, architectural)
1. Multiple research docs per area
2. Detailed implementation plan with code snippets
3. Phase-by-phase execution with compaction between phases

---

## 📚 Source Materials

- **Talk**: "No Vibes Allowed: Solving Hard Problems in Complex Codebases" - AI Engineer Code Summit
- **Repository**: [humanlayer/humanlayer](https://github.com/humanlayer/humanlayer)
- **12 Factor Agents**: [humanlayer/12-factor-agents](https://github.com/humanlayer/12-factor-agents)
- **Advanced Context Engineering**: [humanlayer/advanced-context-engineering-for-coding-agents](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents)

---

## 🏢 About HumanLayer

HumanLayer (YC W24) builds CodeLayer, an agentic IDE for teams to manage AI coding agents. Their methodology has been battle-tested shipping production code to complex codebases including:

- 300k LOC Rust codebase (BAML)
- Their own multi-language monorepo
- Various open-source contributions

**Key Stats from their work:**
- 35,000 lines shipped in 7 hours (estimated 1-2 weeks of work)
- PRs approved by maintainers unfamiliar with the contributor
- Intern shipped 2 PRs on day 1, 10 PRs by day 8

---

*This guide is for Claude Code users looking to maximize their productivity with AI coding agents.*
