<div align="center">

[🏠 Home](README.md) • [📖 Overview](00-OVERVIEW.md) • **03 Agentic Patterns**

━━━━━━━━━●━━━━━━━━━━━━━━━━━━━━━ `3/8`

[← 02 Architecture](02-LAYER-ARCHITECTURE.md) • [04 Use Cases →](04-USE-CASES.md)

</div>

---

# Agentic Patterns

> 7 patterns for building Claude Code agentic systems + 2 implementation mechanisms

## 📑 Table of Contents

| # | Pattern | Description |
|---|---------|-------------|
| 1 | [🏎️ Direct Execution](#pattern-1-️-direct-execution) | Baseline (no orchestration) |
| 2 | [⛓️ Prompt Chaining](#pattern-2-️-prompt-chaining) | Sequential steps |
| 3 | [🚦 Routing](#pattern-3--routing) | Classification & dispatch |
| 4 | [🛤️ Parallelization](#pattern-4-️-parallelization) | Concurrent execution |
| 5 | [🦑 Subagent Orchestration](#pattern-5--subagent-orchestration) | Manager + specialists |
| 6 | [🩻 Evaluator-Optimizer](#pattern-6--evaluator-optimizer) | Iterative refinement |
| 7 | [🐉 Autonomous Agents](#pattern-7--autonomous-agents) | Self-directed execution |
| ⚙️ | [Mechanisms](#mechanisms) | Progressive Skills, Programmatic Orchestration |
| ⚔️ | [Pattern Comparisons](#pattern-comparisons) | Side-by-side VS diagrams |
| 📋 | [Best Practices](#best-practices) | Operational guidelines |

---

## Terminology

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         UNIFIED TERMINOLOGY                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  🐔 Main Agent    → Claude Code orchestrator (the hen that coordinates)    │
│  🐦 Subagent      → Delegated worker spawned via Task (the bird)           │
│  🪺 Spawn (Task)  → Action to create 🐦 subagents (via Task built-in tool) │
│  📚 Skill         → Loaded knowledge that enhances 🐔 capabilities          │
│                                                                             │
│  HIERARCHY: 🙋‍♀️📥 User → 🐔 Main Agent → 🐦 Subagent → 💁‍♀️📤 User              │
│                                                                             │
│  RULE: 🐦 Subagents CANNOT spawn other 🐦 subagents (flat hierarchy)        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Decision Tree

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TD
    classDef question fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#ffffff
    classDef pattern fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef simple fill:#64748b,stroke:#475569,stroke-width:2px,color:#ffffff

    START["🙋‍♀️📥 Task Received"] --> Q1{"Single step?"}:::question

    Q1 -->|Yes| P1["🏎️ Direct Execution"]:::simple
    Q1 -->|No| Q2{"Steps dependent?"}:::question

    Q2 -->|Yes, sequential| P2["⛓️ Prompt Chaining"]:::pattern
    Q2 -->|No, parallel| Q3{"Same or different tasks?"}:::question

    Q3 -->|Same task, different data| P4["🛤️ Parallelization"]:::pattern
    Q3 -->|Different tasks| P5["🦑 Subagent Orchestration"]:::pattern

    Q2 -->|Need classification first| P3["🚦 Routing"]:::pattern

    START --> Q4{"Quality critical?"}:::question
    Q4 -->|Yes, needs iteration| P6["🩻 Evaluator-Optimizer"]:::pattern

    START --> Q5{"Open-ended exploration?"}:::question
    Q5 -->|Yes| P7["🐉 Autonomous Agents"]:::pattern
```

---

## Pattern 1: 🏎️ Direct Execution

### Definition

🐔 Main Agent handles the request directly without spawning 🐦 subagents or complex orchestration.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff

    USER["🙋‍♀️📥 User Request"]:::user --> MA["🐔💭 Main Agent"]:::main
    MA -->|"🐔📤"| OUT["💁‍♀️📤 User Receives"]:::user
```

### When to Use

- Simple, single-step tasks
- No need for specialization
- Quick operations (file read, simple edit, search)

### When NOT to Use

- Complex multi-step workflows
- Tasks requiring multiple specializations
- Large-scale operations

### Example

```
User: "What's in the config.json file?"

🐔 Main Agent:
  - Uses Read tool to read config.json
  - Returns content to user

No 🐦 subagents needed.
```

---

## Pattern 2: ⛓️ Prompt Chaining

### Definition

Breaking a task into sequential steps where each step's output becomes the next step's input.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff

    USER["🙋‍♀️📥 User Request"]:::user --> P1["🐔💭 Step 1"]:::main
    P1 -->|"🐔📤"| P2["🐔💭 Step 2"]:::main
    P2 -->|"🐔📤"| P3["🐔💭 Step 3"]:::main
    P3 -->|"🐔📤"| OUT["💁‍♀️📤 User Receives"]:::user
```

### Use Cases

| Use Case | Example |
|----------|---------|
| Document processing | Extract → Analyze → Summarize |
| Code generation | Plan → Implement → Review |
| Data transformation | Parse → Transform → Validate |

### Example Flow

```
Step 1: "Extract all function names from this code"
        → [list of functions]

Step 2: "For each function, identify parameters and return types"
        → [function signatures]

Step 3: "Generate documentation for each function"
        → [documented code]
```

### When to Use

- Tasks have clear sequential dependencies
- Each step's quality affects the next
- Need checkpoints between steps

### When NOT to Use

- Steps can be done independently (use 🛤️ Parallelization)
- Simple single-step tasks (use 🏎️ Direct Execution)

### Variant: 🧙 Wizard Workflows

Multi-step processes with explicit 🙆‍♀️ user confirmation at each phase using ❓ `AskUserQuestion`.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
stateDiagram-v2
    [*] --> Analysis: 🙋‍♀️📥 User Request

    Analysis --> Confirm1: Present findings
    Confirm1 --> Planning: 🙆‍♀️✅ User approves
    Confirm1 --> Analysis: 🙆‍♀️❓ User requests changes

    Planning --> Confirm2: Present plan
    Confirm2 --> Implementation: 🙆‍♀️✅ User approves
    Confirm2 --> Planning: 🙆‍♀️❓ User requests changes

    Implementation --> Confirm3: Show changes
    Confirm3 --> Verification: 🙆‍♀️✅ User approves
    Confirm3 --> Implementation: 🙆‍♀️❓ User requests changes

    Verification --> [*]: ✅ Complete
```

**Use 🧙 Wizard for:**
- Destructive operations (migrations, deletions)
- Complex refactoring
- Multi-stakeholder decisions

---

## Pattern 3: 🚦 Routing

### Definition

Directing inputs to specialized handlers based on classification or intent.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff
    classDef idle fill:#94a3b8,stroke:#64748b,stroke-width:2px,color:#ffffff

    INPUT["🙋‍♀️📥 User Request"]:::user --> ROUTER{"🐔🚦 Route"}:::main

    ROUTER -.->|"Type A"| HA["🐦💤 Handler A"]:::idle
    ROUTER -->|"🐔🪺 Type B"| HB["🐦⚡ Handler B"]:::subagent
    ROUTER -.->|"Type C"| HC["🐦💤 Handler C"]:::idle
    ROUTER -.->|"Unknown"| DEFAULT["🐔💤 Default"]:::idle

    HB -->|"🐦📤"| FINAL["💁‍♀️📤 User Receives"]:::user
```

### Key Insight

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  🚦 ROUTING: Choose ONE branch                                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Logic: if/else, switch/case                                               │
│  Question: "Where should I send this?"                                      │
│  Result: Single output from chosen handler                                  │
│                                                                             │
│  Analogy: Train switch → One train takes ONE track                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Use Cases

| Use Case | Example |
|----------|---------|
| Customer support | Bug → Tech Team, Billing → Finance |
| Code tasks | Bug fix → Debugger, New feature → Builder |
| Content | Question → Q&A, Task → Executor |

### When to Use

- Inputs have distinct types requiring different handling
- Specialized expertise improves quality
- Clear classification criteria exist

### When NOT to Use

- All inputs require same processing
- Classification is unreliable

---

## Pattern 4: 🛤️ Parallelization

### Definition

Executing independent tasks simultaneously and merging results.

### Core Concept

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff

    IN["🙋‍♀️📥"]:::user --> SPLIT["🐔🔀 Split"]:::main
    SPLIT -->|"🐔🪺"| A["🐦⚡"]:::parallel
    SPLIT -->|"🐔🪺"| B["🐦⚡"]:::parallel
    SPLIT -->|"🐔🪺"| C["🐦⚡"]:::parallel
    A -->|"🐦📤"| MERGE["🐔🌀 Merge"]:::main
    B -->|"🐦📤"| MERGE
    C -->|"🐦📤"| MERGE
    MERGE -->|"🐔📤"| OUT["💁‍♀️📤"]:::user
```

### Key Insight

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  ⚠️  IMPORTANT: Parallelization vs Subagent Orchestration                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  In Parallelization, all spawned subagents are IDENTICAL.                   │
│  Same prompt, same capabilities. They are INTERCHANGEABLE.                  │
│                                                                             │
│  🛤️ Parallelization:        🐦⚡ = 🐦⚡ = 🐦⚡   (clones)                      │
│  🦑 Subagent Orchestration:  🐦🔒 ≠ 🐦⚡ ≠ 🐦🎨   (specialists)                │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2 Types of Parallelization

#### Type 1: 🛤️ Sectioning (Split DATA)

Split large data into chunks, process each chunk the same way, combine all results.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff

    S_IN["🙋‍♀️📥 100 files"]:::user --> S_SPLIT["🐔🛤️"]:::main
    S_SPLIT -->|"🐔🪺"| S1["🐦⚡ Files 1-50"]:::parallel
    S_SPLIT -->|"🐔🪺"| S2["🐦⚡ Files 51-100"]:::parallel
    S1 -->|"🐦📤"| S_MERGE["🐔🌀 Combine ALL"]:::main
    S2 -->|"🐦📤"| S_MERGE
    S_MERGE -->|"🐔📤"| S_OUT["💁‍♀️📤"]:::user
```

#### Type 2: 🗳️ Voting (Same TASK, pick BEST)

Run the same task multiple times, compare results, pick the best one.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff
    classDef wizard fill:#14b8a6,stroke:#0d9488,stroke-width:2px,color:#ffffff
    classDef success fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff

    V_IN["🙋‍♀️📥 Write headline"]:::user --> V_COPY["🐔🔀 3 attempts"]:::main
    V_COPY -->|"🐔🪺"| V1["🐦⚡ Version A"]:::parallel
    V_COPY -->|"🐔🪺"| V2["🐦⚡ Version B"]:::parallel
    V_COPY -->|"🐔🪺"| V3["🐦⚡ Version C"]:::parallel
    V1 -->|"🐦📤"| VOTE{"🐔🗳️ Compare"}:::wizard
    V2 -->|"🐦📤"| VOTE
    V3 -->|"🐦📤"| VOTE
    VOTE -->|"🐔✅ B wins"| BEST["🏆 Best"]:::success
```

### Summary

| Type | Workers | Input | Output |
|------|---------|-------|--------|
| **🛤️ Sectioning** | IDENTICAL | Different DATA chunks | Combine ALL |
| **🗳️ Voting** | IDENTICAL | Same DATA | Pick ONE best |

### Variant: 🚂 Parallel Tool Calling

Execute multiple independent 🔧 tool calls in a single message for efficiency.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff
    classDef state fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff

    MA["🐔 Main Agent"]:::main -->|Single Message| TOOLS

    subgraph TOOLS["🚂 Parallel Tool Calls"]
        T1["🔧 Read file1.ts"]
        T2["🔧 Read file2.ts"]
        T3["🔧 Read file3.ts"]
    end

    T1 --> RESULTS["✅ All Results"]:::state
    T2 --> RESULTS
    T3 --> RESULTS

    RESULTS --> MA

    style TOOLS fill:#dbeafe,stroke:#3b82f6,stroke-width:2px
```

**Implementation:**
```python
# Single message with multiple parallel 🔧 tool calls
[
    Read(file_path="/src/auth.ts"),
    Read(file_path="/src/user.ts"),
    Read(file_path="/src/session.ts"),
    Grep(pattern="password", path="/src")
]
# All execute concurrently, results returned together
```

### Variant: 🧬 Master-Clone

Spawn multiple isolated 🐦 instances handling independent domains with no shared state.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff
    classDef state fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff

    MA["🐔 Main Agent"]:::main

    MA -->|"Context: fr-FR"| C1["🐦 Clone: fr-FR"]:::subagent
    MA -->|"Context: es-ES"| C2["🐦 Clone: es-ES"]:::subagent
    MA -->|"Context: de-DE"| C3["🐦 Clone: de-DE"]:::subagent

    C1 -->|9 files| R1[Result: fr-FR]
    C2 -->|9 files| R2[Result: es-ES]
    C3 -->|9 files| R3[Result: de-DE]

    R1 --> MERGE["✅ Merge Results"]:::state
    R2 --> MERGE
    R3 --> MERGE

    MERGE --> MA
```

**Key Characteristic:** Each 🐦 clone operates in complete isolation (no shared memory, no inter-clone communication).

### When to Use

- Tasks have no dependencies on each other
- Speed is important
- Resources allow concurrent execution

### When NOT to Use

- Tasks depend on each other's output
- Sequential order matters

---

## Pattern 5: 🦑 Subagent Orchestration

### Definition

🐔 Main Agent 🪺 spawns specialized 🐦 subagents via the `Task` tool to handle complex, domain-specific tasks.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff

    INPUT["🙋‍♀️📥 Review this PR"]:::user --> ORCH["🐔🦑 Orchestrator"]:::main

    ORCH -->|"🐔🪺 Check vulns"| W1["🐦🔒 Security Expert"]:::subagent
    ORCH -->|"🐔🪺 Check perf"| W2["🐦⚡ Performance Expert"]:::subagent
    ORCH -->|"🐔🪺 Check style"| W3["🐦🎨 Style Expert"]:::subagent

    W1 -->|"🐦📤 2 SQLi found"| SYNTH["🐔🌀 Synthesize"]:::main
    W2 -->|"🐦📤 O(n²) loop"| SYNTH
    W3 -->|"🐦📤 3 violations"| SYNTH

    SYNTH -->|"🐔📤"| OUTPUT["💁‍♀️📤 Final Report"]:::user
```

### Key Insight

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  🦑 SUBAGENT ORCHESTRATION: Different specialists                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Each 🐦 subagent has a DIFFERENT expertise and does a DIFFERENT task.     │
│                                                                             │
│  Analogy: Hospital team → Different experts collaborate                     │
│           (Chef + Pastry + Sommelier, not 3 cooks making same recipe)      │
│                                                                             │
│  Compare to 🛤️ Parallelization:                                            │
│  - Parallelization: Same worker + Different data (assembly line)           │
│  - Orchestration: Different workers + Same data (expert team)              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Main Agent Responsibilities

| Responsibility | Description |
|----------------|-------------|
| **Decomposition** | Break complex task into subtasks |
| **Assignment** | Route subtasks to appropriate 🐦 subagents |
| **Monitoring** | Track 🐦 subagent progress |
| **Synthesis** | Combine results into coherent output |

### 🐦 Subagent Definition

```markdown
# .claude/agents/code-reviewer.md

---
name: code-reviewer
description: Reviews code for quality, security, and best practices
tools: Read, Grep, Glob
---

You are a senior code reviewer specializing in security and quality.

## Your Task
Review the provided code and report:
1. Security vulnerabilities
2. Performance issues
3. Code quality concerns
4. Suggested improvements

## Output Format
- ❌ CRITICAL: Issues requiring immediate attention
- ⚠️ WARNING: Should be addressed
- ℹ️ INFO: Suggestions for improvement
```

### Critical Rules

| Rule | Explanation |
|------|-------------|
| **No nested subagents** | 🐦 Subagents cannot spawn other 🐦 subagents |
| **Isolated context** | Each 🐦 subagent starts fresh, no shared memory |
| **Report to orchestrator** | Results flow back to 🐔 Main Agent only |

### When to Use

- Complex tasks require multiple specializations
- Workers can operate independently
- Need centralized coordination

### When NOT to Use

- Simple tasks not worth decomposition overhead
- Workers need heavy inter-communication

---

## Pattern 6: 🩻 Evaluator-Optimizer

### Definition

Generate candidates, evaluate them, and iteratively improve until quality threshold is met.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef data fill:#06b6d4,stroke:#0891b2,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef wizard fill:#14b8a6,stroke:#0d9488,stroke-width:2px,color:#ffffff
    classDef success fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff
    classDef error fill:#ef4444,stroke:#dc2626,stroke-width:2px,color:#ffffff

    INPUT["🙋‍♀️📥 Task"]:::user --> GEN["🐔💭 Generate"]:::main
    GEN --> CAND["🐔📤 Candidate"]:::data
    CAND --> EVAL{"🐔🩻 Evaluate"}:::wizard

    EVAL -->|"🐔✅ Pass"| OUTPUT["💁‍♀️📤 Output"]:::success
    EVAL -->|"🐔❌ Fail"| FEEDBACK["🐔🔄 Feedback"]:::error
    FEEDBACK --> GEN
```

### Detailed Flow

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
sequenceDiagram
    participant U as 🙋‍♀️ User
    participant G as 🐔💭 Generator
    participant E as 🐔🩻 Evaluator

    U->>G: 🙋‍♀️📥 Request
    loop 🔄 Until quality threshold
        G->>G: 🐔💭 Generate candidate
        G->>E: 🐔📤 Submit for evaluation
        E->>E: 🐔👀 Score candidate
        alt ✅ Score >= threshold
            E->>U: 💁‍♀️📤 Accept
        else ❌ Score < threshold
            E->>G: 🐔🔄 Feedback for improvement
        end
    end
```

### Evaluation Criteria Examples

| Domain | Criteria |
|--------|----------|
| **Code** | Tests pass, linting clean, no security issues |
| **Text** | Clarity score, factual accuracy, tone match |
| **Design** | Usability score, accessibility, consistency |

### Example: Code Generation

```
Generator: Write function to parse CSV

Attempt 1: Basic implementation
Evaluator: "Missing error handling for malformed input"

Attempt 2: Added try/catch
Evaluator: "Not handling empty files"

Attempt 3: Complete implementation
Evaluator: "Pass - all criteria met"
```

### When to Use

- Quality is critical
- Clear evaluation criteria exist
- Iteration improves results

### When NOT to Use

- First attempt is usually good enough
- No clear quality metrics
- Time constraints prevent iteration

---

## Pattern 7: 🐉 Autonomous Agents

### Definition

Long-running agents that independently plan, execute, and adapt based on environment feedback.

### Diagram

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef data fill:#06b6d4,stroke:#0891b2,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef state fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff
    classDef wizard fill:#14b8a6,stroke:#0d9488,stroke-width:2px,color:#ffffff

    GOAL["🙋‍♀️📥 Goal"]:::user --> PLAN["🐔📋 Plan"]:::main
    PLAN --> ACT["🐔⚡ Act"]:::state
    ACT --> ENV["🌍 Environment"]:::data
    ENV --> OBSERVE["🐔👀 Observe"]:::data
    OBSERVE --> REFLECT{"🐔💭 Reflect"}:::wizard

    REFLECT -->|"🐔🔄 Adjust"| PLAN
    REFLECT -->|"🐔▶️ Continue"| ACT
    REFLECT -->|"🐔✅ Done"| DONE["💁‍♀️📤 Result"]:::user
```

### Agent Loop

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
stateDiagram-v2
    [*] --> Planning: 🙋‍♀️📥 Receive goal
    Planning --> Executing: 🐔📋 Create plan
    Executing --> Observing: 🐔⚡ Take action
    Observing --> Reflecting: 🐔👀 Get feedback
    Reflecting --> Planning: 🐔🔄 Adjust
    Reflecting --> Executing: 🐔▶️ Continue
    Reflecting --> [*]: 💁‍♀️📤 Goal achieved
```

### Characteristics

| Characteristic | Description |
|----------------|-------------|
| **Goal-directed** | Works toward specified objective |
| **Adaptive** | Adjusts based on feedback |
| **Self-directed** | Decides next actions independently |
| **Persistent** | Continues until goal achieved or failure |

### Example: Bug Investigation

```
Goal: "Fix the login timeout bug"

Agent:
1. PLAN: Need to find where timeout is set
2. ACT: Search codebase for "timeout" in auth
3. OBSERVE: Found 3 locations
4. REFLECT: Most likely in session config
5. ACT: Read session config file
6. OBSERVE: Default timeout is 30 minutes
7. REFLECT: User reported issue after 5 minutes
8. ACT: Check if there's an override
9. ...continues until resolved...
```

### Risk Management

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef state fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff
    classDef error fill:#ef4444,stroke:#dc2626,stroke-width:2px,color:#ffffff

    subgraph Guardrails["🛡️ Guardrails"]
        LIMIT["⏱️ Iteration Limit"]:::error
        APPROVAL["🙆‍♀️✅ Human Approval"]:::user
        SCOPE["🔒 Action Scope"]:::error
        ROLLBACK["↩️ Rollback Capability"]:::error
    end

    AGENT["🐔 Main Agent (autonomous)"]:::main --> Guardrails
    Guardrails --> SAFE["✅ Safe Execution"]:::state

    style Guardrails fill:#fef2f2,stroke:#ef4444,stroke-width:2px
```

### Variant: 🖥️ Multi-Window Context

Implement checkpointing to save 💾 state and resume from interruptions in long-running workflows.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef checkpoint fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#ffffff
    classDef state fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff

    subgraph Session1["Session 1"]
        S1P1["🏗️ Phase 1"] --> S1CP["🖥️ Checkpoint"]:::checkpoint
        S1CP --> S1P2["🔗 Phase 2"]
        S1P2 --> INTERRUPT["❌ Interrupt"]
    end

    subgraph Session2["Session 2 (Resume)"]
        RESUME["🔄 Resume"] --> S2P2["Continue Phase 2"]
        S2P2 --> S2P3["📝 Phase 3"]
        S2P3 --> DONE["✅ Complete"]
    end

    S1CP -.->|💾 State saved| STATE[("💾 State Store")]:::state
    STATE -.->|💾 State loaded| RESUME

    style Session1 fill:#fef2f2,stroke:#ef4444,stroke-width:2px
    style Session2 fill:#ecfdf5,stroke:#10b981,stroke-width:2px
```

**Use 🖥️ Multi-Window for:**
- Large-scale generation (1000+ files)
- Long research tasks
- Multi-day workflows
- Error recovery

### When to Use

- Open-ended exploration tasks
- Environment feedback is rich
- Human oversight is available

### When NOT to Use

- Predictable tasks with known steps
- No rollback capability
- Tight time constraints

---

## Mechanisms

These are **implementation mechanisms** in Claude Code, not agentic patterns themselves.

### 📚 Progressive Skills

Load 📚 skills on-demand to enhance 🐔 agent capabilities for specific task types.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef skill fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef decision fill:#f59e0b,stroke:#d97706,stroke-width:2px,color:#ffffff

    REQ["🙋‍♀️📥 User Request"] --> MA["🐔 Main Agent"]:::main
    MA --> CHECK{"📚 Match Skills?"}:::decision

    CHECK -->|TDD Task| TDD["📚 test-driven-development"]:::skill
    CHECK -->|Debug Task| DEBUG["📚 systematic-debugging"]:::skill
    CHECK -->|Review Task| REVIEW["📚 code-review"]:::skill
    CHECK -->|None| DIRECT[Direct Execution]

    TDD --> EXEC["✅ Enhanced Execution"]
    DEBUG --> EXEC
    REVIEW --> EXEC
    DIRECT --> EXEC
```

**Purpose:** Route execution through specialized methodologies.

**📚 Skill Definition:**
```markdown
# .claude/skills/test-driven-development/SKILL.md

---
description: Use when implementing features - write tests first
---

# 📚 Test-Driven Development

## When to Use
- New feature implementation
- Bug fixes (write test that catches bug first)

## Methodology
1. 🏗️ RED: Write failing test
2. 🔗 GREEN: Minimal code to pass
3. 📝 REFACTOR: Clean while green
```

### 🎛️ Programmatic Orchestration

External code controls 🐔 agent invocation and workflow logic rather than pure prompt-based control.

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff

    CODE["🎛️ External Code"]:::user --> LOOP{For each item}

    LOOP --> INVOKE["🐔 Invoke Claude"]:::subagent
    INVOKE --> RESULT[Get Result]
    RESULT --> PROCESS["🎛️ Process in Code"]
    PROCESS --> LOOP

    LOOP -->|Done| FINAL["✅ Final Output"]
```

**Purpose:** ⛓️ Prompt Chaining with external control (CI/CD, batch processing, API automation).

**Implementation:**
```python
# 🎛️ External Python script orchestrating Claude
import anthropic

client = anthropic.Anthropic()

locales = ["fr-FR", "es-ES", "de-DE"]
results = []

for locale in locales:
    response = client.messages.create(
        model="claude-sonnet-4-20250514",
        messages=[{"role": "user", "content": f"Generate for {locale}"}]
    )
    results.append({"locale": locale, "content": response.content})
    save_progress(results)  # 🎛️ Code-controlled checkpointing
```

---

## Pattern Comparisons

### 🚦 Routing vs 🛤️ Parallelization

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff
    classDef idle fill:#94a3b8,stroke:#64748b,stroke-width:2px,color:#ffffff

    subgraph ROUTING["🚦 Routing: Choose ONE"]
        R_IN["🙋‍♀️📥"]:::user --> R_DECIDE{"🐔🚦"}:::main
        R_DECIDE -.-> R_A["🐦💤 A"]:::idle
        R_DECIDE --> R_B["🐦⚡ B"]:::subagent
        R_DECIDE -.-> R_C["🐦💤 C"]:::idle
        R_B --> R_OUT["💁‍♀️📤"]:::user
    end

    subgraph PARALLEL["🛤️ Parallelization: Run ALL"]
        P_IN["🙋‍♀️📥"]:::user --> P_SPLIT["🐔🔀"]:::main
        P_SPLIT --> P_A["🐦⚡ A"]:::parallel
        P_SPLIT --> P_B["🐦⚡ B"]:::parallel
        P_SPLIT --> P_C["🐦⚡ C"]:::parallel
        P_A --> P_MERGE["🐔🌀"]:::main
        P_B --> P_MERGE
        P_C --> P_MERGE
        P_MERGE --> P_OUT["💁‍♀️📤"]:::user
    end

    style ROUTING fill:#fef2f2,stroke:#ef4444,stroke-width:2px
    style PARALLEL fill:#eff6ff,stroke:#3b82f6,stroke-width:2px
```

| Aspect | 🚦 Routing | 🛤️ Parallelization |
|--------|-----------|-------------------|
| **Action** | Choose **ONE** branch | Execute **ALL** branches |
| **Logic** | `if/else`, `switch/case` | `fork/join`, `Promise.all` |
| **Question** | "Where should I send this?" | "How can I do all this at once?" |
| **Result** | Single output from chosen handler | Multiple outputs merged |

### 🛤️ Parallelization vs 🦑 Subagent Orchestration

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef subagent fill:#ec4899,stroke:#db2777,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff

    subgraph PARALLEL["🛤️ Parallelization: SAME Task"]
        P_IN["🙋‍♀️📥"]:::user --> P_SPLIT["🐔🔀"]:::main
        P_SPLIT --> P_A["🐦⚡ Chunk 1"]:::parallel
        P_SPLIT --> P_B["🐦⚡ Chunk 2"]:::parallel
        P_SPLIT --> P_C["🐦⚡ Chunk 3"]:::parallel
        P_A --> P_MERGE["🐔🌀"]:::main
        P_B --> P_MERGE
        P_C --> P_MERGE
        P_MERGE --> P_OUT["💁‍♀️📤"]:::user
    end

    subgraph ORCH["🦑 Orchestration: DIFFERENT Tasks"]
        O_IN["🙋‍♀️📥"]:::user --> O_MAIN["🐔🪺"]:::main
        O_MAIN --> O_A["🐦🔒 Security"]:::subagent
        O_MAIN --> O_B["🐦⚡ Perf"]:::subagent
        O_MAIN --> O_C["🐦🎨 Style"]:::subagent
        O_A --> O_SYNTH["🐔🌀"]:::main
        O_B --> O_SYNTH
        O_C --> O_SYNTH
        O_SYNTH --> O_OUT["💁‍♀️📤"]:::user
    end

    style PARALLEL fill:#eff6ff,stroke:#3b82f6,stroke-width:2px
    style ORCH fill:#fdf4ff,stroke:#ec4899,stroke-width:2px
```

| Aspect | 🛤️ Parallelization | 🦑 Subagent Orchestration |
|--------|-------------------|------------------------|
| **Workers** | **Interchangeable** (same skill) | **Specialized** (different skills) |
| **Task type** | **Identical** task on different data | **Different** tasks on same data |
| **Decision** | **Static** (predefined split) | **Dynamic** (orchestrator decides) |
| **Example** | 3 cooks make same recipe | Chef + Pastry + Sommelier |

---

## Pattern Summary

```
┌──────────────────────────┬─────────────┬─────────────┬──────────────┬───────────┐
│ Pattern                  │ Complexity  │ Parallelism │ Human-Loop   │ Iteration │
├──────────────────────────┼─────────────┼─────────────┼──────────────┼───────────┤
│ 🏎️ Direct Execution      │ None        │ None        │ None         │ None      │
│ ⛓️ Prompt Chaining        │ Low         │ None        │ Optional     │ Linear    │
│ 🚦 Routing                │ Low         │ None        │ None         │ None      │
│ 🛤️ Parallelization        │ Medium      │ High        │ Optional     │ None      │
│ 🦑 Subagent Orchestration │ High        │ High        │ Optional     │ As needed │
│ 🩻 Evaluator-Optimizer    │ Medium      │ Optional    │ Optional     │ Loop      │
│ 🐉 Autonomous Agent       │ Very High   │ Variable    │ Recommended  │ Adaptive  │
└──────────────────────────┴─────────────┴─────────────┴──────────────┴───────────┘
```

---

## Best Practices

### Permission Modes

Control how 🐦 Subagents request permissions for tool usage.

| Mode | Behavior | Use Case |
|------|----------|----------|
| `default` | Asks permission for each tool call | Read-only operations, validation |
| `acceptEdits` | Auto-approves Write/Edit operations | Generation after 🧙 user confirmation |
| `bypassPermissions` | All tools auto-approved | Trusted autonomous workflows |

**Best Practice:** Use 🧙 Wizard confirmation before spawning subagents with elevated permissions.

### Parallelization Limits

| Type | Recommended Max | Risk if Exceeded |
|------|-----------------|------------------|
| 🐦 Concurrent Subagents | **10-15** | Context overflow, memory pressure |
| 🔌 MCP calls per subagent | **5** | Rate limit errors |
| 🪺 Task calls per message | **10** | API limits, timeouts |

**Batching Strategy:**
```
# Instead of 39 parallel subagents:
Wave 1: 10 🐦 subagents
Wave 2: 10 🐦 subagents
Wave 3: 10 🐦 subagents
Wave 4:  9 🐦 subagents
```

### Context Management

Use `/compact` between major phases to prevent overflow:

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart LR
    classDef wave fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff
    classDef compact fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef checkpoint fill:#10b981,stroke:#059669,stroke-width:2px,color:#ffffff

    W1["🚂 Wave 1"]:::wave
    CP1["🖥️ Checkpoint"]:::checkpoint
    C1["/compact"]:::compact

    W2["🚂 Wave 2"]:::wave
    CP2["🖥️ Checkpoint"]:::checkpoint
    C2["/compact"]:::compact

    W3["🚂 Wave 3"]:::wave
    DONE["✅ Complete"]:::checkpoint

    W1 --> CP1 --> C1 --> W2 --> CP2 --> C2 --> W3 --> DONE
```

**Pattern:** Checkpoint → Compact → Resume

---

## Combining Patterns

These patterns are building blocks that combine:

```mermaid
%%{init: {'theme': 'base', 'themeVariables': {'lineColor': '#64748b'}}}%%
flowchart TB
    classDef user fill:#6366f1,stroke:#4f46e5,stroke-width:2px,color:#ffffff
    classDef wizard fill:#14b8a6,stroke:#0d9488,stroke-width:2px,color:#ffffff
    classDef main fill:#8b5cf6,stroke:#7c3aed,stroke-width:2px,color:#ffffff
    classDef parallel fill:#3b82f6,stroke:#2563eb,stroke-width:2px,color:#ffffff

    subgraph Combined["🔗 Complex System"]
        R["🐔🚦 Routing"]:::wizard --> OW["🐔🎭 Orchestrator"]:::main
        OW -->|"🐔🪺"| P["🐦⚡ Parallel Workers"]:::parallel
        P -->|"🐦📤"| EO["🐔🩻 Evaluator"]:::wizard
    end

    INPUT["🙋‍♀️📥 Input"]:::user --> R
    EO -->|"🐔📤"| OUTPUT["💁‍♀️📤 Output"]:::user

    style Combined fill:#f8fafc,stroke:#e2e8f0,stroke-width:2px
```

### Example: Code Review Pipeline

1. **🚦 Routing**: Classify code change type
2. **🦑 Subagent Orchestration**: Assign to security, perf, style workers
3. **🛤️ Parallelization**: Workers run concurrently
4. **🩻 Evaluator-Optimizer**: Iterate on feedback if issues found

---

<div align="center">

**━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━**

[← 02 Architecture](02-LAYER-ARCHITECTURE.md) • [🏠 Home](README.md) • [04 Use Cases →](04-USE-CASES.md)

</div>
