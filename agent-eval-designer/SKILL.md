---
name: agent-eval-designer
description: Use when an AI agent needs evaluation scenarios, acceptance criteria, regression cases, adversarial tests, tool-failure tests, memory tests, or a pass/fail rubric before implementation, release, or optimization.
---

# Agent Eval Designer

## Overview

Turn desired agent behavior into concrete eval scenarios. Focus on what the agent must do, how success is judged, and which failures should be caught before release.

Core principle: agent quality is not a vibe; it is a set of scenarios with expected behavior, observable traces, and pass/fail criteria.

## Eval Set Minimum

Every non-trivial agent needs at least:

1. **Happy path**: user asks a clear supported task.
2. **Tool failure**: one required tool errors, times out, or returns partial data.
3. **Ambiguous input**: request lacks details and the agent should ask a clarifying question.
4. **Unsafe or destructive request**: agent should refuse, ask confirmation, or narrow scope.
5. **Regression case**: a known previous failure must stay fixed.

Add memory, cost, latency, or multi-step planning cases when relevant.

## Eval Design Workflow

1. Identify the agent goal and boundaries.
2. List behaviors that would prove the agent works.
3. List failure modes that would be expensive or confusing.
4. Convert each behavior into a scenario:
   - input
   - setup/state
   - expected actions
   - forbidden actions
   - observable trace
   - pass/fail criteria
5. Recommend the smallest runnable harness or manual table.

## Scenario Format

```markdown
### EVAL-001: Short name

- **Purpose:** what risk this catches
- **Input:** user request or event
- **Setup:** files, memory, tools, mocks, permissions
- **Expected behavior:** what the agent should do
- **Forbidden behavior:** what must not happen
- **Trace checks:** tool calls, memory reads/writes, stop condition
- **Pass criteria:** objective checks
- **Fail examples:** concrete bad outputs or actions
```

## Rubric

Use a 0-2 score when binary pass/fail is too coarse:

- **2**: correct behavior, correct tool use, useful trace, no unsafe actions
- **1**: partially correct but missing clarification, trace, or recovery
- **0**: wrong answer, unsafe action, unnecessary destructive tool call, or loop

Require at least one hard fail condition per eval.

## Agent-Specific Eval Ideas

### Tool-Using Agents

- chooses correct tool from similar tools
- avoids tool call when answer is already known
- validates tool arguments
- recovers from tool failure

### Memory Agents

- remembers user-approved durable facts
- ignores irrelevant old memory
- corrects stale memory
- avoids storing sensitive content

### Coding Agents

- writes tests before risky changes
- does not overwrite unrelated user changes
- verifies build/test output before completion
- handles ambiguous requirements by asking or narrowing scope

### Research Agents

- uses primary sources
- labels inference vs source-backed fact
- avoids stale or uncited claims
- reports uncertainty and gaps

## Output Format

```markdown
**Eval Plan**
- Goal:
- Scope:
- Harness:

**Scenarios**
1. ...

**Coverage Gaps**
- ...

**Recommended First Run**
- ...
```

## Relationship To Other Skills

- Use `agent-explorer` when expected behavior depends on unknown APIs or docs.
- Use `agent-reviewer` to judge whether the eval plan covers release risks.
- Use `agent-supervisor` for a combined oversight pass.
