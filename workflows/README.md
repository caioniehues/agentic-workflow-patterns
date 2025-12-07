<div align="center">

# Workflows

[🏠 Home](../README.md) • [🦄 Foundations](../foundations/) • **⚙️ Workflows** • [🐉 Agents](../agents/) • [🛠️ Implementation](../implementation/) • [🗺️ Guides](../guides/)

</div>

---

> **TL;DR:** Predefined orchestration paths where code controls the flow. From simple baseline to complex multi-step processes.

---

## What are Workflows?

> "**Workflows** are systems where LLMs and tools are orchestrated through **predefined code paths**."
> — Anthropic, Building Effective Agents

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              WORKFLOWS                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  🏎️ Baseline           → Single LLM call, no orchestration                  │
│  ⛓️ Prompt Chaining    → Sequential steps, output → input                   │
│  🚦 Routing            → Classify then dispatch                             │
│  🛤️ Parallelization    → Concurrent independent tasks                       │
│  🦑 Orchestrator       → Delegate to specialized workers                    │
│  🩻 Evaluator          → Iterative improvement via feedback                 │
│                                                                             │
│  KEY: Code controls the flow (predictable, debuggable)                      │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Workflow Index

| # | Workflow | Emoji | Description | Complexity |
|:-:|----------|:-----:|-------------|:----------:|
| 0 | [Baseline](00-baseline.md) | 🏎️ | Single LLM call, no orchestration | None |
| 1 | [Prompt Chaining](01-prompt-chaining.md) | ⛓️ | Sequential steps, output→input | Low |
| 2 | [Routing](02-routing.md) | 🚦 | Classify then dispatch | Low |
| 3 | [Parallelization](03-parallelization.md) | 🛤️ | Concurrent independent tasks | Medium |
| 4 | [Orchestrator-Workers](04-orchestrator-workers.md) | 🦑 | Delegate to specialized subagents | High |
| 5 | [Evaluator-Optimizer](05-evaluator-optimizer.md) | 🩻 | Iterative improvement via feedback | Medium |

---

## Quick Decision

```
Simple Task (1 step)          → 🏎️ Baseline
Sequential (2-4 steps)        → ⛓️ Prompt Chaining
Categorized inputs            → 🚦 Routing
Independent parallel tasks    → 🛤️ Parallelization
Complex with specialists      → 🦑 Orchestrator-Workers
Quality iteration needed      → 🩻 Evaluator-Optimizer
```

---

## Workflow Variants

| Variant | Parent | Emoji | Description |
|---------|--------|:-----:|-------------|
| **Wizard Workflow** | ⛓️ Prompt Chaining | 🧙 | Human checkpoints via AskUserQuestion |
| **Parallel Tool Calling** | 🛤️ Parallelization | 🚂 | Multiple tools in single response |
| **Master-Clone** | 🛤️ Parallelization | 🧬 | Same agent, parallel instances |

---

<div align="center">

[🏠 Home](../README.md) • [🦄 Foundations](../foundations/) • **⚙️ Workflows** • [🐉 Agents](../agents/) • [🛠️ Implementation](../implementation/) • [🗺️ Guides](../guides/)

</div>
