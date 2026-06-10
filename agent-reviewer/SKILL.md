---
name: agent-reviewer
description: Use when an AI agent design, implementation, prompt, tool interface, memory plan, control loop, or completion claim needs independent review for architecture risks, safety gaps, missing tests, overengineering, or unverified assumptions.
---

# Agent Reviewer

## Overview

Review agent work as an independent critic. Prioritize bugs, risks, missing evals, unsafe tool boundaries, weak observability, and claims without evidence. Do not rewrite everything by default.

Core principle: an agent design is credible only when its boundaries, failure modes, and verification evidence are explicit.

## Review Stance

Lead with findings. Keep praise brief and secondary.

Use severity labels:

- **Blocker**: must fix before implementation or release
- **Risk**: likely to cause agent failures or maintenance pain
- **Gap**: missing evidence, unclear contract, or untested behavior
- **Suggestion**: useful but optional

## Review Checklist

### Architecture

- Is the agent goal narrow enough?
- Is there a clear stop condition?
- Are planner, executor, tools, memory, and UI/API boundaries separated?
- Does the design avoid unnecessary abstractions?

### Tool Boundary

- Are tool inputs structured and validated?
- Are read/write permissions explicit?
- Are destructive actions gated by confirmation?
- Are timeouts, retries, and tool errors represented?

### Memory And State

- Is cross-run memory justified?
- Is stale or wrong memory correctable?
- Is sensitive or irrelevant data excluded?
- Could explicit project files or user input replace memory?

### Observability

- Can a developer inspect the model decision, tool call, observation, and final answer?
- Are traces or logs useful without leaking secrets?
- Can failures be reproduced?

### Verification

- Are there evals for happy path, tool failure, and ambiguous input?
- Were tests or manual traces actually run?
- Are limitations documented?

## Output Format

```markdown
**Findings**
- **Blocker:** ...
- **Risk:** ...
- **Gap:** ...
- **Suggestion:** ...

**Required Verification**
- ...

**Questions**
- ...

**Next Action**
- ...
```

If there are no blockers, say that clearly, then list residual risks.

## Common Review Traps

| Trap | Response |
|---|---|
| "The model can decide" | Ask what prompt, schema, state, and eval make that reliable. |
| "We will add tests later" | Mark as a completion blocker. |
| "This memory may be useful" | Require a specific future behavior it improves. |
| "The tool is internal" | Internal tools still need validation and failure handling. |
| "It worked manually once" | Ask for repeatable scenario and trace. |

## Relationship To Other Skills

- Use `agent-explorer` first when facts or APIs are unknown.
- Use `agent-eval-designer` when eval coverage is weak.
- Use `agent-supervisor` when the user wants one combined oversight pass.
