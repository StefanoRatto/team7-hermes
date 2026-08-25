---
name: t7-grill-me
description: "Security engagement grilling — relentless interview about engagement plans until every branch resolved."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [grilling, interview, engagement-planning, scope, offensive-security]
    category: t7
---

# t7-grill-me — Security Engagement Grilling

Adapted from mattpocock/skills/grill-me for offensive security operations.

## When to Use

Trigger keywords: `grill me`, `grill-me`, `stress test this plan`, `challenge my scope`, `poke holes`

- Before any engagement starts (Phase 0)
- Before Phase 2 exploitation (stress-test the attack plan)
- When scope changes mid-engagement
- When the operator presents a new plan or approach

## Rules

- Ask questions ONE AT A TIME. Wait for feedback before continuing.
- If a question can be answered by exploring the target environment, explore it instead of asking.
- For each question, state your recommended answer with reasoning.
- Do not accept vague answers. Push for specificity.
- Cover: scope boundaries, threat model, crown jewels, attack priorities, time budget, rules of engagement, communication protocol, evidence requirements, legal constraints.

## Process

1. Identify the plan or decision tree to grill
2. Walk down each branch, resolving dependencies between decisions one by one
3. For each branch point, ask the forcing question and provide your recommendation
4. Push back on vague answers (max 2 pushbacks per question)
5. Document resolved decisions as premises
6. Present all premises at the end for confirmation

## Integration

- Strengthens the Engagement Discovery Protocol (6 forcing questions) with open-ended grilling
- Can be invoked at any phase boundary
- Produces refined scope that feeds into engagement planning
- Delegate execution to t7-planner-agent when appropriate

## Anti-Sycophancy

- Never say "that's a reasonable scope" without evidence
- Take a position on every answer
- If operator says "test everything," push back with effort estimates
- If operator downscopes aggressively, name blind spots being accepted
