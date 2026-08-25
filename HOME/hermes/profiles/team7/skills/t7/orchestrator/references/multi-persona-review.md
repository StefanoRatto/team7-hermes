# Multi-Persona Finding Review

After Phase 1 findings are synthesized (before Phase 2 begins), dispatch parallel review personas to validate, score, and deduplicate findings.

## Review Personas

Dispatch in parallel against the Phase 1 findings synthesis:

| Persona | Question It Answers | When |
|---------|--------------------|----|
| correctness-reviewer | Is this finding real? Is the evidence valid? Does the PoC demonstrate the claim? | Always |
| exploitability-reviewer | Can this be exploited in context? Are there mitigating controls? | CRITICAL/HIGH findings |
| impact-assessor | What is the realistic business impact? What data/systems are reachable? | Always |
| false-positive-hunter | Adversarial — actively try to DISPROVE the finding | CRITICAL, or "too clean" findings |
| variant-detector | Same pattern in other endpoints, parameters, or methods? | All confirmed findings |
| remediation-validator | Is the remediation correct, complete, and specific? | Report generation phase |

## Dedup Pipeline

```
Step 1: FINGERPRINT
  fingerprint = hash(target + endpoint + vulnerability_type + first_50_chars_of_evidence)

Step 2: GROUP by fingerprint similarity
  - Exact match → merge, keep highest confidence anchor
  - Near match (same target+endpoint+vuln but different evidence) → flag for manual review
  - No match → pass through

Step 3: CROSS-REVIEWER PROMOTION
  - Both "confirmed" → promote anchor (50→75, 75→100)
  - One "confirmed" + one "needs investigation" → keep current, note disagreement
  - Both "false positive" → set anchor to 0, remove with documentation

Step 4: MERGE
  - Deduplicated findings get richest evidence from all sources
  - Conflicting severity → resolve to MORE SEVERE (conservative)
  - Conflicting remediation → present both with reasoning
```

## Remediation Classification

| Class | Meaning | Report Presentation |
|-------|---------|---------------------|
| immediate | Zero risk (update TLS config, remove default creds) | "Implement immediately" |
| requires_review | Needs change management (auth change, firewall rule) | "Requires review and testing" |
| manual_only | Architecture change, code rewrite | "Requires design review" |
| advisory | Informational, no specific fix | "Consider for security roadmap" |

## Sequencing

Cross-Validation runs FIRST (per-finding specialist re-test).
Multi-Persona Review runs SECOND (batch assessment of cross-validated findings).
