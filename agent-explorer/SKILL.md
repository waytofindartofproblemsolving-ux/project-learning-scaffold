---
name: agent-explorer
description: Use when building or reviewing an AI agent requires focused investigation of documentation, APIs, SDK behavior, codebase structure, examples, tool contracts, memory patterns, eval frameworks, or unknown implementation details before design or coding.
---

# Agent Explorer

## Overview

Investigate facts for agent work. Do not design the agent, write implementation code, or make unsupported guesses. Produce a sourced briefing that the main agent or reviewer can rely on.

Core principle: an explorer reduces uncertainty by reading primary sources and local code, then reports evidence, gaps, and confidence.

## Responsiveness Rule

This is a focused investigation skill, not a background agent. Do not spawn subagents, wait for other roles, or chain to another `agent-*` skill automatically.

If invoked together with other specialist skills for a generic "supervise this task" request, do not produce a full separate report unless there is an explicit unknown fact to investigate. Prefer one short section:

```markdown
**Exploration Note**
- Checked:
- Useful fact:
- Remaining unknown:
```

## Scope

Use this skill to answer questions such as:

- Which API or SDK pattern should this agent use?
- Where does the current code implement tools, memory, state, or evals?
- What examples exist for streaming, tool calls, retries, traces, or guardrails?
- What constraints or deprecations matter before implementation?
- What is unknown and needs a decision?

Do not:

- patch files
- choose architecture for the main agent
- invent APIs from memory
- summarize without citing files or URLs

## Investigation Workflow

1. Restate the narrow question.
2. Identify source classes:
   - local files and tests
   - official docs or repository examples
   - existing project conventions
3. Read only what is needed.
4. Extract exact facts: names, paths, signatures, config keys, command names, and observed behavior.
5. Report with confidence and gaps.

## Report Format

```markdown
**Question**
- ...

**Sources Read**
- `path/or/url`: what was checked

**Findings**
- ...

**Relevant Snippets or Locations**
- `file:line` or URL section:

**Constraints**
- ...

**Unknowns**
- ...

**Confidence**
- High/Medium/Low because ...
```

## Quality Bar

Every actionable claim needs a source. If a fact comes from inference, label it as inference.

Good:

```markdown
- The tool schema is defined in `src/tools/search.ts`; it validates `query` as a required string.
- Inference: this means planner output should produce structured JSON, not free text.
```

Bad:

```markdown
- The SDK probably supports retries.
```

## Optional Next Skills

- Recommend `agent-reviewer` when unresolved architecture tradeoffs need critique.
- Recommend `agent-eval-designer` when behavior scenarios and pass/fail criteria are the next deliverable.
- Recommend `project-learning-scaffold` when the user wants to learn the project concepts while building.
