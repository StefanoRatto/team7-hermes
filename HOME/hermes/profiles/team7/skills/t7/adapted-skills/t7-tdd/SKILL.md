---
name: t7-tdd
description: "Exploit-Verify-Refine loop — vertical-slice methodology for exploit development and finding verification."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [tdd, exploit-development, verification, vertical-slices, offensive-security]
    category: t7
---

# t7-tdd — Exploit-Verify-Refine Loop

Adapted from mattpocock/skills/tdd for offensive security operations.

## When to Use

Trigger keywords: `tdd approach`, `test-driven`, `verify first`, `red green refactor`, `vertical slices`

- When developing exploits for confirmed vulnerabilities
- When verifying findings before reporting
- When the operator wants disciplined exploitation methodology

## Anti-Pattern: Horizontal Slices

DO NOT scan everything first, then exploit everything. This is horizontal slicing — it produces stale findings and wasted effort.

```
WRONG (horizontal):
  SCAN:    target1, target2, target3, target4, target5
  EXPLOIT: target1, target2, target3, target4, target5

RIGHT (vertical):
  SCAN+VERIFY: target1 → confirm → exploit → document
  SCAN+VERIFY: target2 → confirm → exploit → document
  SCAN+VERIFY: target3 → confirm → exploit → document
```

## Exploit-Verify-Refine Loop

1. **Hypothesis (RED)**: "This endpoint is vulnerable to X because of Y."

2. **Probe**: Send a witness payload that should trigger the vulnerability. Observe: does it behave as predicted?

3. **Confirm (GREEN)**: If probe succeeds, write the working PoC. Minimal payload that demonstrates exploitability.

4. **Refine**: After confirmation, look for variants. Same pattern in other endpoints? Different parameters? Different HTTP methods?

## Rules

- One finding at a time. Confirm before moving to the next.
- Only enough exploitation to prove the finding. Do not over-exploit.
- Keep probes focused on observable behavior (HTTP responses, timing, error messages).
- A finding that survives a target update is a good finding. A finding that breaks on framework update is fragile.

## Integration

- This is the micro-methodology within Phase 2
- t7-exploitation-agent follows this loop for each vulnerability
- Cross-validates with browser-based verification for web findings
- Feeds confirmed findings into evidence collection
