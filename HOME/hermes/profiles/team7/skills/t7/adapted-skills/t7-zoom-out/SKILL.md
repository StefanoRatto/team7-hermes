---
name: t7-zoom-out
description: "Architecture abstraction — go up a layer, map modules, callers, data flows, trust boundaries."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [architecture, abstraction, mapping, trust-boundaries, offensive-security]
    category: t7
---

# t7-zoom-out — Architecture Abstraction

Adapted from mattpocock/skills/zoom-out for offensive security operations.

## When to Use

Trigger keywords: `zoom out`, `bigger picture`, `how does this fit`, `map the architecture`, `context for this area`

- When deep in a specific area and need the broader context
- When mapping attack surface before exploitation
- When understanding trust boundary crossings
- When identifying where user input enters sensitive operations

## Rules

- This is a direct instruction, not an interview. Produce the map immediately.
- Show: module relationships, caller chains, data flow direction, trust boundary crossings, authentication checkpoints
- Security perspective: highlight where user input enters, where it crosses trust boundaries, where it reaches sensitive operations
- Delegate to t7-code-review-agent (source code context) or t7-recon-agent (live infrastructure context)

## Output Format

```
[External] ──HTTP──> [Reverse Proxy (nginx)] ──/api──> [App Server]
                                                          │
                                              ┌───────────┼───────────┐
                                              │           │           │
                                          [Auth MW]   [Business]   [Admin]
                                              │           │           │
                                              └───────────┼───────────┘
                                                          │
                                                    [Database]

Trust Boundaries:
  TB1: External → Reverse Proxy (TLS termination)
  TB2: App Server → Database (credential required)
  TB3: Business → Admin (role check)

User Input Entry Points:
  - POST /api/login (body: email, password)
  - GET /api/search?q= (query parameter, reaches DB)
  - PUT /api/profile (body: name, avatar_url — reaches S3)
```
