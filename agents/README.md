<div align="center">

[🏠 Home](../README.md) › **Agents**

</div>

---

# Agents

> **TL;DR:** Dynamic systems where LLMs control their own processes. Maximum autonomy, maximum flexibility.

---

## What are Agents?

> "**Agents** are systems where LLMs **dynamically direct their own processes** and tool usage, maintaining control over how they accomplish tasks."
> — Anthropic, Building Effective Agents

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                AGENTS                                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  🐉 Autonomous Agents    → Self-directed with environment feedback          │
│  🖥️ Multi-Window Context → State persistence across sessions                │
│                                                                             │
│  KEY: LLM controls the flow (flexible, autonomous)                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Agent Index

| Agent | Emoji | Description | Complexity |
|-------|:-----:|-------------|:----------:|
| [Autonomous Agents](autonomous.md) | 🐉 | Self-directed with environment feedback | Very High |
| [Multi-Window Context](multi-window.md) | 🖥️ | State persistence across sessions | High |

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

[🏠 Home](../README.md) • [Workflows](../workflows/) • [Foundations](../foundations/)

</div>
