# Evidence Protocol & Delivery Workflow

## Mandatory Evidence Format

Every finding MUST include:
- **Command**: Exact command used to discover/exploit
- **Output**: Raw output/evidence
- **Timestamp**: When the evidence was collected
- **Confidence Anchor**: 0/25/50/75/100

## Confidence-Anchored Finding Scoring

| Anchor | Meaning | Evidence Required |
|--------|---------|-------------------|
| 0 | Theoretical — pattern match only | Static analysis flag, no verification |
| 25 | Indicator — something looks wrong | Unusual response, version match to CVE but no PoC |
| 50 | Observed — evidence exists but not exploited | Confirmed vulnerable version, misconfig verified |
| 75 | Reproduced — exploit executed successfully | Working PoC with evidence |
| 100 | Independently verified — second agent confirmed | Two different agents/techniques both confirm |

## Cross-Reviewer Promotion

When 2+ agents report same finding (matched by target + endpoint + vuln type):
- 25 → 50 (two agents see indicators)
- 50 → 75 (two agents agree it exists)
- 75 → 100 (two agents independently exploited)

## Suppression Gate

| Severity | Min Anchor for Report | Below Threshold |
|----------|----------------------|-----------------|
| CRITICAL (CVSS 9-10) | 50 | Suppress with note: "indicator observed, recommend manual verification" |
| HIGH (CVSS 7-8.9) | 75 | Suppress entirely |
| MEDIUM (CVSS 4-6.9) | 75 | Suppress entirely |
| LOW (CVSS 0.1-3.9) | 75 | Suppress entirely |

## Finding Quality Gate

Before presenting ANY finding:

| Check | Requirement |
|-------|-------------|
| Evidence | Exact command + raw output |
| Severity | CVSS score or clear reasoning |
| Business Impact | What an attacker could achieve |
| Reproducibility | Steps for another tester to reproduce |
| No Vague Language | No "might be vulnerable" without proof |

## Report Quality Scoring (0-10 per dimension)

| Dimension | What a 10 Looks Like |
|-----------|---------------------|
| Evidence Quality | Every finding has exact command, raw output, timestamp |
| Specificity | "SQLi via boolean-blind on GET /api/users?id=1' AND 1=1-- on PostgreSQL 14.2" |
| Impact Clarity | "Attacker can read all 50,000 customer records including SSNs" |
| Reproducibility | Numbered steps, expected output at each step |
| Remediation Quality | Parameterized query example in target's language |
| Completeness | Every endpoint tested or explicitly out of scope |

## Security Slop (reject these patterns)
- "The application should implement input validation" (generic)
- "Consider implementing rate limiting" (without evidence of abuse)
- "Follow the principle of least privilege" (without specific violation)
- Remediation that restates the finding instead of providing a fix
- "This could potentially allow an attacker to..." (prove it or don't claim it)

## Delivery Pipeline

```
Step 1 — READINESS CHECK
  All objectives COMPLETE or DEFERRED? All CRITICAL/HIGH cross-validated?
  Evidence for every finding? If any fail: BLOCKED.

Step 2 — EVIDENCE INTEGRITY AUDIT (t7-evidence-collection-agent)
  Hash all evidence files. Flag findings without evidence as UNVERIFIED.

Step 3 — REPORT GENERATION (t7-report-generation-agent)
  Executive summary, technical findings, remediation roadmap.
  Apply Report Quality Scoring. No vague language.

Step 4 — REPORT REVIEW (independent agent)
  All findings represented? Severity justified? Remediation actionable?

Step 5 — PACKAGE ASSEMBLY
  Report + evidence + scope doc + manifest with SHA-256 hashes.

Step 6 — DELIVERY CONFIRMATION
  Present summary to operator. Operator approves or requests revisions.
```

## Remediation Classification

| Class | Meaning |
|-------|---------|
| immediate | Zero risk — update TLS config, remove default creds |
| requires_review | Needs change management — auth mechanism change |
| manual_only | Architecture change, code rewrite |
| advisory | Informational, no specific fix |
