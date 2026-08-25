---
name: t7-design-attack-paths
description: "Parallel attack path design — spawn multiple sub-agents with radically different strategies, then compare."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [attack-paths, parallel-design, strategy, offensive-security]
    category: t7
---

# t7-design-attack-paths — Parallel Attack Path Design

Adapted from mattpocock/skills/design-an-interface ("Design It Twice" from A Philosophy of Software Design) for offensive security.

## When to Use

Trigger keywords: `design attack paths`, `explore approaches`, `design it twice`, `parallel designs`, `alternative approaches`

- When Phase 1 reveals multiple viable attack paths
- When the operator needs help choosing between approaches
- When a single approach has failed and alternatives are needed

## Principle

Your first attack approach is unlikely to be the best. Generate multiple radically different attack strategies using parallel sub-agents, then compare.

## Workflow

### 1. Gather Requirements
- What is the objective?
- What access do we have?
- What constraints exist?
- What defenses are known?

### 2. Generate Designs (Parallel Sub-Agents)

Spawn 3+ sub-agents simultaneously via `delegate_task(tasks=[...])`. Each MUST produce a RADICALLY DIFFERENT approach:

- **Agent 1**: "Minimize noise — stealth-first approach"
- **Agent 2**: "Maximize speed — fastest path to objective"
- **Agent 3**: "Maximize coverage — broadest attack surface"
- **Agent 4**: "Unconventional — what would an APT do differently?"

### 3. Present Approaches

For each, show:
- Attack path summary (3-5 steps)
- Tools/techniques required
- Estimated time
- Detection risk (low/medium/high)
- Dependencies and prerequisites

### 4. Compare

| Criterion | Approach 1 | Approach 2 | Approach 3 |
|-----------|-----------|-----------|-----------|
| Steps to objective | ? | ? | ? |
| Detection risk | ? | ? | ? |
| Time estimate | ? | ? | ? |
| Success likelihood | ? | ? | ? |
| Blast radius | ? | ? | ? |

### 5. Synthesize

Often the best approach combines elements from multiple designs. Present a hybrid recommendation with reasoning.

## Integration

- Delegate each variant to t7-oracle-agent or t7-exploitation-agent
- Use when Phase 1 synthesis reveals multiple viable paths
- Output feeds into Autoplan Pipeline or direct Phase 2 execution
