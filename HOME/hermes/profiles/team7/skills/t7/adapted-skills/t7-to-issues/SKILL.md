---
name: t7-to-issues
description: "Engagement task vertical slicing — break plans into independently-grabbable AFK/HITL vertical slices."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [task-decomposition, vertical-slices, planning, delegation, offensive-security]
    category: t7
---

# t7-to-issues — Engagement Task Vertical Slicing

Adapted from mattpocock/skills/to-issues for offensive security operations.

## When to Use

Trigger keywords: `break this down`, `to-issues`, `create tasks`, `vertical slices`, `decompose the plan`

- When an engagement plan needs decomposition into executable tasks
- When planning parallel agent delegations
- When the operator wants to see the work broken into manageable pieces

## Vertical Slice Rules

- Each slice delivers a narrow but COMPLETE path through reconnaissance, exploitation, and documentation
- A completed slice produces a verified finding with evidence
- Prefer many thin slices over few thick ones
- Maximize parallelism — mark which slices can run simultaneously

## Slice Types

| Type | Meaning | Example |
|------|---------|---------|
| AFK | Can be delegated to a sub-agent without operator interaction | "Scan /api/* endpoints for IDOR" |
| HITL | Requires operator decision (scope, risk, legal) | "Should we attempt credential stuffing against production auth?" |

Prefer AFK over HITL where possible.

## Process

1. **Gather context** from conversation and engagement state
2. **Draft vertical slices** with:
   - Title (action-oriented)
   - Type (AFK/HITL)
   - Blocked-by (dependencies on other slices)
   - Objectives addressed
   - Agent to delegate to
3. **Present to operator** for approval. Iterate on granularity.
4. **Create as todos** with dependency ordering

## Output Format

```
## Slice 1: Enumerate all API endpoints [AFK]
  Agent: t7-recon-agent
  Blocked-by: none
  Objective: Map attack surface
  Done when: endpoint list with methods documented

## Slice 2: Test IDOR on user-facing endpoints [AFK]
  Agent: t7-pentesterweb
  Blocked-by: Slice 1
  Objective: Find access control vulnerabilities
  Done when: each endpoint tested, findings with evidence

## Slice 3: Confirm scope for internal network pivot [HITL]
  Agent: operator decision
  Blocked-by: Slice 2 (if IDOR gives internal access)
  Objective: Determine if lateral movement is authorized
  Done when: operator confirms or denies
```

## Integration

- Each AFK slice becomes a `delegate_task` call
- HITL slices are presented to operator via `clarify`
- Dependency ordering determines execution sequence
- Track slices using the Hermes `todo` tool (one item per slice, status: pending/in_progress/completed)
- Feeds directly into todo management for tracking
