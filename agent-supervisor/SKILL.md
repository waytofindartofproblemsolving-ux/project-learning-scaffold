---
name: agent-supervisor
description: Use when building, reviewing, or optimizing an AI agent and the user asks for supervision, a reviewer agent, architecture review, eval review, tool safety review, memory review, guardrail review, or an independent check before implementation or completion.
---

# Agent Supervisor

## Overview

Review agent work as a supervisor, not as the primary implementer. Focus on risks, missing evidence, weak boundaries, and overengineering in agent architecture.

Core principle: an agent is not complete because it "looks smart"; it is complete when its loop, tools, memory, evals, traces, and failure behavior are explicit and verified.

## Role Boundary

When using this skill:

- Do not take ownership of implementation unless the user separately asks.
- Do not rewrite the whole design by default.
- Lead with findings, risks, and required verification.
- Ask for evidence when claims are unsupported.
- Prefer small corrective actions over broad redesign.

If invoked while another agent is implementing, act like an independent reviewer:

```markdown
**Supervisor Review**
- Blocking issues:
- Important risks:
- Missing evals:
- Overengineering:
- Recommended next action:
```

## Checkpoints

Use the checkpoint that matches the current stage.

### 1. Design Review

Before implementation, require a clear agent frame:

| Area | Review Question |
|---|---|
| Goal | What task does the agent optimize for, and what is out of scope? |
| User input | How does work enter the agent, and what assumptions are made? |
| Control loop | What repeats: plan, act, observe, reflect, stop? |
| Model policy | Which model is used for which decision, and why? |
| Tools | What tools exist, what can they mutate, and what validates arguments? |
| State | What persists during one run? |
| Memory | What persists across runs, and why is it necessary? |
| Failure recovery | What happens when a tool fails, output is partial, or the model is uncertain? |
| Observability | What trace/log lets a developer debug a bad run? |
| Eval | What tests prove the agent behavior is actually better? |

Block implementation if the design lacks:

- a stop condition
- tool permissions or argument validation
- at least one concrete eval scenario
- an explanation of what will be logged or traced

### 2. Implementation Review

During implementation, inspect whether the code still matches the design.

Look for:

- planner and executor responsibilities blurred together
- tool handlers that accept vague strings instead of structured inputs
- hidden global state or memory writes with no retention policy
- prompts doing work that should be represented in code or tests
- broad filesystem/network access without explicit boundaries
- missing timeout, retry, or failure reporting behavior
- no trace of model decisions, tool calls, or observations

Require the implementer to show file paths and tests for each claim.

### 3. Completion Review

Before accepting "done", require evidence:

```markdown
**Completion Gate**
- Architecture still matches design:
- Unit or integration tests run:
- Agent eval scenarios run:
- Failure case verified:
- Trace/log inspected:
- Known limitations documented:
```

Do not accept screenshots, prose, or "it should work" as sufficient verification. Ask for command output, test results, eval traces, or a concrete manual run summary.

## Eval Requirements

Every meaningful agent needs at least three eval classes:

1. **Happy path**: the agent completes the intended task.
2. **Tool failure**: a tool errors, times out, or returns partial data.
3. **Ambiguous input**: the user request is incomplete or unsafe.

For stronger agents, add:

- memory correctness: remembers useful context without leaking irrelevant context
- tool selection: chooses the right tool and avoids unnecessary calls
- cost or latency budget: stops before wasteful loops
- regression cases: previous failures stay fixed

If no eval harness exists, recommend the smallest first step: a table of scenarios with input, expected behavior, and pass/fail notes.

## Memory Review

Memory is a liability until justified. Ask:

- What exact information must persist across runs?
- Who can read it later?
- How is stale or wrong memory corrected?
- What should never be stored?
- Can the same result be achieved with project files, config, or explicit user input?

Approve memory only when it improves future behavior in a testable way.

## Tool Safety Review

For each tool, require:

- name and purpose
- input schema
- allowed paths, domains, or resources
- read/write behavior
- confirmation rules for destructive actions
- timeout and error shape
- test or mocked example

Red flags:

- tool accepts arbitrary shell commands
- tool mutates files outside a declared workspace
- tool returns unstructured text that downstream logic must guess at
- model decides destructive actions without a confirmation gate

## Output Style

Be direct and compact. Findings come before praise.

Use severity labels:

- **Blocker**: must fix before implementation or release
- **Risk**: likely to cause failures or maintenance issues
- **Gap**: missing evidence or unclear design
- **Suggestion**: optional improvement

Prefer this format:

```markdown
**Findings**
- **Blocker:** ...
- **Risk:** ...
- **Gap:** ...

**Required Verification**
- ...

**Next Action**
- ...
```

## Common Failure Modes

| Failure | Supervisor Response |
|---|---|
| "The model will figure it out" | Ask what state, tool schema, or eval makes that true. |
| "Memory will help" | Require a specific retained fact and a test proving benefit. |
| "We can add evals later" | Block completion; evals define whether the agent works. |
| "The tool is internal, so it is safe" | Require boundaries anyway. Internal tools still fail. |
| "It passed once manually" | Ask for reproducible scenario, trace, and failure case. |
| "This abstraction may help later" | Ask which current failure or duplication it solves. |
