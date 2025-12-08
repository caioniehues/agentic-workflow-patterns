<div align="center">

# 🐔 Autonomous Agent

[🏠 Home](../README.md) • [🦄 Foundations](../foundations/) • [⚙️ Workflows](../workflows/) • **🐔 Autonomous** • [🛠️ Implementation](../implementation/) • [🗺️ Guides](../guides/)

</div>

---

> **TL;DR:** THE alternative to workflows. When the LLM controls its own process instead of following predefined paths.

---

## What is the Autonomous Agent?

> "**Agents** are systems where LLMs **dynamically direct their own processes** and tool usage, maintaining control over how they accomplish tasks."
> — Anthropic, Building Effective Agents

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      🐔 AUTONOMOUS AGENT                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  The ALTERNATIVE to Workflows when:                                         │
│  • Steps are unpredictable                                                  │
│  • Path is open-ended                                                       │
│  • LLM needs to decide what to do next                                      │
│                                                                             │
│  ⚙️ Workflows = code controls the flow (predefined paths)                   │
│  🐔 Autonomous = LLM controls the flow (dynamic decisions)                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Index

| Pattern | Emoji | Description | Complexity |
|---------|:-----:|-------------|:----------:|
| [Autonomous Agent](autonomous.md) | 🐔 | Self-directed with environment feedback | Very High |
| [Multi-Window Context](multi-window.md) | 🖥️ | State persistence across sessions (variant) | High |

---

## Workflows vs Agents

| Aspect | Workflows | Agents |
|--------|-----------|--------|
| **Control** | Code controls the flow | LLM controls the flow |
| **Path** | Predefined | Dynamic |
| **Predictability** | High | Low |
| **Debuggability** | Easy | Hard |
| **Flexibility** | Limited | Maximum |
| **Use case** | Known steps | Open-ended problems |

---

## When to Use Agents

Agents can be used for **open-ended problems** where:
- It's difficult or impossible to predict the required number of steps
- You can't hardcode a fixed path
- The LLM will potentially operate for many turns
- You have some level of trust in its decision-making

---

<div align="center">

[🏠 Home](../README.md) • [🦄 Foundations](../foundations/) • [⚙️ Workflows](../workflows/) • **🐔 Autonomous** • [🛠️ Implementation](../implementation/) • [🗺️ Guides](../guides/)

</div>
