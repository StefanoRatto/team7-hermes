---
name: t7-to-prd
description: "Engagement scope synthesis — synthesize conversation context into formal engagement scope document."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [scope, synthesis, engagement-planning, documentation, offensive-security]
    category: t7
---

# t7-to-prd — Engagement Scope Synthesis

Adapted from mattpocock/skills/to-prd for offensive security operations.

## When to Use

Trigger keywords: `synthesize scope`, `write the scope doc`, `to-prd`, `create engagement scope`

- When the conversation has already covered scope and needs formalization
- Fast-path alternative to full Engagement Discovery Protocol
- When operator wants to capture current understanding as a document

## Process

1. **Explore** the target environment to understand current state (if not already done)
2. **Sketch** the major attack phases and target components. Identify opportunities for deep testing (small attack surface, significant impact).
3. **Check** with operator that these components match expectations.
4. **Write** the engagement scope document.

## Output Template

```markdown
# Engagement Scope: [Target Name]

## Threat Statement
[Who would attack, what they'd gain, adversary profile]

## Scope Items
- S-001: [target/system/endpoint] — [priority: HIGH/MEDIUM/LOW]
- S-002: ...

## Prioritized Objectives
1. [Most critical objective]
2. [Second priority]
3. ...

## Attack Approach
[Depth vs breadth decision, phase strategy]

## Testing Decisions
- [Key methodology choices with reasoning]

## Out of Scope
- [Explicitly excluded items with justification]

## Time Estimates
| Phase | Estimated Duration |
|-------|-------------------|
| Recon | ... |
| Analysis | ... |
| Exploitation | ... |
| Reporting | ... |
```

## Rules

- Do NOT interview the operator -- synthesize what is already known
- If critical information is missing, ask at most ONE question
- Save output to `deliverables/engagement-scope.md`
- Feeds into Autoplan Pipeline Phase D (Scope Lock)
