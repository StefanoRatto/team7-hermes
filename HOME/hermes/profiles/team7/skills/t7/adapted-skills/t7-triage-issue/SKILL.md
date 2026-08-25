---
name: t7-triage-issue
description: "Security finding triage — investigate root cause, produce verification plan with test-verify cycles."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [triage, investigation, root-cause, debugging, offensive-security]
    category: t7
---

# t7-triage-issue — Security Finding Triage

Adapted from mattpocock/skills/triage-issue for offensive security operations.

## When to Use

Trigger keywords: `triage this`, `investigate this finding`, `root cause`, `why did this fail`

- When an exploit fails unexpectedly
- When a finding is ambiguous or contradictory
- When investigating a reported vulnerability
- When the three-strike rule triggers reassessment

## Process

1. **Capture**: Get a brief description. If unclear, ask ONE question: "What is the problem?" Start investigating immediately.

2. **Explore and Diagnose**: Delegate to t7-investigator-agent. Find WHERE it manifests, WHAT code/config path is involved, WHY it fails, and WHAT related patterns exist.

3. **Identify Fix Approach**: Minimal change needed, which modules/interfaces are affected, what behaviors need verification.

4. **Design Verification Plan**: Ordered list of test-verify cycles:
   - Each cycle: describe a specific probe that captures the broken behavior
   - Then describe the minimal fix/alternative approach
   - Tests verify behavior through observable interfaces, not assumptions
   - One test at a time, vertical slices
   - Findings should be DURABLE — describe behaviors and contracts, not internal file paths

5. **Document**: Write the finding with root cause analysis, verification plan, and acceptance criteria.

## Integration

- Feeds into the Investigation Protocol (Iron Law)
- When the three-strike rule triggers, this skill structures the investigation
- Output feeds into wisdom accumulation as FAILURES or GOTCHAS
