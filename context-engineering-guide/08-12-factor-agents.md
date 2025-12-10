# The 12 Factor Agents

> Principles for building reliable and scalable LLM applications

Based on [12 Factor Apps](https://12factor.net), these principles guide building production-grade AI agents that actually work in customer-facing applications.

---

## Overview

Most "AI Agents" in production aren't fully agentic. They're mostly deterministic code with LLM steps at key points. The 12 Factor Agents principles help you build LLM-powered software that's reliable enough for production.

> "The fastest way I've seen for builders to get good AI software in the hands of customers is to take small, modular concepts from agent building, and incorporate them into their existing product."

---

## The 12 Factors

### Factor 1: Natural Language to Tool Calls
**Principle:** Use LLMs as command generators, not prose writers.

### Factor 2: Own Your Prompts
**Principle:** Don't outsource prompt engineering to a framework.

### Factor 3: Own Your Context Window
**Principle:** The context window is the state. Manage it deliberately.

### Factor 4: Tools Are Just Structured Outputs
**Principle:** Tool definitions are prompting too.

### Factor 5: Unify Execution State and Business State
**Principle:** No parallel state systems to synchronize.

### Factor 6: Launch/Pause/Resume with Simple APIs
**Principle:** Agents should be controllable like any other service.

### Factor 7: Contact Humans with Tool Calls
**Principle:** Human-in-the-loop as a first-class pattern.

### Factor 8: Own Your Control Flow
**Principle:** Explicit execution paths, not delegated loops.

### Factor 9: Compact Errors into Context Window
**Principle:** Distill failures into concise context, not verbose logs.

### Factor 10: Small, Focused Agents
**Principle:** Specialized agents reduce complexity and increase reliability.

### Factor 11: Trigger from Anywhere
**Principle:** Meet users where they are.

### Factor 12: Make Your Agent a Stateless Reducer
**Principle:** Agents as pure functions: input state → output state.

---

## Factor 13 (Bonus): Pre-fetch All Context You Might Need
**Principle:** Retrieve information upfront, not mid-execution.

---

## Resources

- **Repository:** [humanlayer/12-factor-agents](https://github.com/humanlayer/12-factor-agents)
- **Talk:** [AI Engineer World's Fair presentation](https://www.youtube.com/watch?v=8kMaTybvDUw)

---

## Key Takeaways

1. **LLMs are stateless** - Context window IS the state
2. **Own your primitives** - Prompts, context, control flow
3. **Small and focused** - Beats big and complex
4. **Humans in the loop** - As a first-class pattern
5. **Pure functions** - Input state → Output state
