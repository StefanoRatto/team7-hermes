# Traceability ID System

Every engagement artifact gets a typed ID for end-to-end traceability.

## ID Types

| Prefix | Meaning | Format | Example |
|--------|---------|--------|---------|
| S- | Scope Item | S-NNN | S-001 (in-scope target/system) |
| AP- | Attack Path | AP-NNN | AP-003 (identified attack path) |
| F- | Finding | F-NNN | F-012 (vulnerability finding) |
| E- | Evidence | E-NNN | E-045 (evidence artifact) |

## Usage Rules

- Every scope item in `deliverables/engagement-scope.md` gets an S-ID
- Every identified attack path gets an AP-ID (references which S-IDs it targets)
- Every finding gets an F-ID (references which AP-ID led to it)
- Every evidence file gets an E-ID (references which F-ID it supports)

## Traceability Chain

```
S-001 (target) → AP-003 (attack path) → F-012 (finding) → E-045 (evidence)
```

Every finding must trace back to a scope item. Orphaned findings (no S-ID ancestor) indicate scope creep — flag and confirm with operator.

## In Delegation Prompts

When delegating to sub-agents, include relevant IDs:
```
TARGET: S-003 (api.target.com)
ATTACK PATH: AP-007 (SSRF via PDF generation endpoint)
PRODUCE: Finding with F-ID, evidence with E-IDs
```

## In Reports

Findings reference their IDs so the reader can trace from executive summary (F-IDs) → technical detail (E-IDs) → scope authorization (S-IDs).
