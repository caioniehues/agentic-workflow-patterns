# Tool Taxonomy Redesign

> Design document for the 3-category tool classification system

**Date**: 2025-01-27
**Status**: Implemented

---

## Problem Statement

The original documentation used `🔌` (plug) emoji inconsistently:
- Used for MCP Tools specifically in the style guide
- Used for ALL tools generically in Layer 4 documentation

This created confusion because `🔌` specifically represents MCP (Model Context Protocol), not built-in Claude Code tools.

---

## Solution: 3-Category Tool Taxonomy

### Categories

```
┌─────────────────────────────────────────────────────────────────┐
│                    LAYER 4: EXECUTION                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  🛠️ Native Tools         🔌 MCP Tools        🖐️ User Interaction│
│  ────────────────        ────────────        ─────────────────  │
│  Read, Write, Edit       Context7            ❓ AskUserQuestion │
│  Bash, Glob, Grep        Perplexity          📋 TodoWrite       │
│  WebFetch, WebSearch     Firecrawl                              │
│                          Custom MCPs                            │
│                                                                 │
│  📤 Task tool → spawn 🤖 Subagents (orchestration)              │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Emoji Assignments

| Category | Emoji | Color | Hex |
|----------|-------|-------|-----|
| **Native Tools** | 🛠️ | Slate | `#64748b` |
| **MCP Tools** | 🔌 | Amber | `#f59e0b` |
| **User Interaction** | 🖐️ | Teal | `#14b8a6` |

### Native Tool Sub-categories (Optional)

For precision in diagrams when needed:

| Sub-category | Emoji | Tools |
|--------------|-------|-------|
| Read Operations | 🛠️👀 | Read, Glob, Grep |
| Write Operations | 🛠️✏️ | Write, Edit, NotebookEdit |
| System Operations | 🛠️💻 | Bash, BashOutput, KillShell |
| Web Operations | 🛠️🌐 | WebFetch, WebSearch |

---

## Design Decisions

### Why 🛠️ for Native Tools?
- "Toolbox" metaphor - complete set of built-in tools
- Visually distinct from 🔌 (external/plug-in)
- Simple and recognizable

### Why 🖐️ for User Interaction?
- "Human hand" represents human-in-the-loop
- Distinct from 🧙 Wizard (pattern, not tool category)
- Alternative 🪄 rejected - too close to 🧙 (same "magic" semantic space)

### Why ⚡ for Layer 4?
- "Execution" concept - where work happens
- Previously 🔌 was overloaded (MCP + Layer)
- Now clearly distinct: ⚡ = Layer, 🔌 = MCP Tools only

---

## Files Updated

1. `08-STYLE-GUIDE.md` - Added 3 tool categories, CSS variables, classDefs
2. `02-LAYER-ARCHITECTURE.md` - Updated Layer 4 section, mindmap, tables
3. `AthenaKNW README.md` - Updated glossary and architecture diagram

---

## Backward Compatibility

- `classDef tool` kept as alias for `mcpTool`
- Existing diagrams using `:::tool` still render correctly (amber/MCP style)
- New diagrams should use explicit `nativeTool`, `mcpTool`, `userInteraction`
