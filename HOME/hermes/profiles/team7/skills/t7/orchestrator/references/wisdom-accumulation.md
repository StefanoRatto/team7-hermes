# Wisdom Accumulation Protocol

Cross-agent learning system. After each sub-agent returns, extract key learnings and pass them to ALL subsequent sub-agents.

## Extraction Process

After EVERY sub-agent completes, extract and categorize:

| Category | What to Extract | Example |
|----------|----------------|---------|
| CONVENTIONS | Target-specific patterns | "Target uses nginx reverse proxy on port 8443" |
| SUCCESSES | Techniques that worked | "SQLi confirmed on /api/users with union-based injection" |
| FAILURES | Approaches that failed and why | "Brute force blocked after 5 attempts — rate limiting active" |
| GOTCHAS | Unexpected behaviors or traps | "WAF silently drops requests with 'select' keyword" |
| CREDENTIALS | Discovered credentials/tokens | "Admin JWT: eyJ... (expires in 1h, RS256)" |
| ARCHITECTURE | Infrastructure details learned | "Backend is AWS ECS behind ALB, us-east-1" |

## Injection Format

Add WISDOM section to ALL subsequent delegation prompts:

```
WISDOM (accumulated from prior agents):
- [SUCCESS] t7-recon-agent: Target runs Apache 2.4.49 on port 443 (CVE-2021-41773 applicable)
- [FAILED] t7-auth-bypass-agent: Brute force blocked after 5 attempts, rate limiting at 10 req/min
- [CONVENTION] Target uses JWT with RS256, tokens expire in 1h, issued by auth.target.com
- [GOTCHA] WAF blocks union-based SQLi but allows boolean-based blind
- [ARCHITECTURE] Backend: AWS ECS, RDS PostgreSQL, S3 bucket "target-assets-prod"
```

## When to Accumulate

- After EVERY sub-agent returns (extract immediately)
- Before EVERY new delegation (inject accumulated wisdom)
- At phase boundaries (consolidate and prune stale wisdom)

## Wisdom Pruning

Keep wisdom compact. At each phase boundary:
- Remove findings superseded by later, more detailed findings
- Consolidate duplicate entries
- Keep the active wisdom list under 15 entries

## Compound Knowledge Store (Persistent)

For Standard/Deep engagements, also write learnings as structured markdown files:

```
deliverables/learnings/
  exploitation-techniques/
  waf-bypass-patterns/
  architecture-patterns/
  dead-ends/
  tool-configurations/
  credential-patterns/
  remediation-patterns/
  recon-patterns/
```

Each file has YAML frontmatter:
```yaml
---
title: "WAF Bypass via Boolean-Blind SQLi"
type: technique
target_class: web-application
technology: ["wkhtmltopdf", "postgresql", "nginx-waf"]
attack_type: sql-injection
tags: ["sqli", "waf-bypass", "boolean-blind"]
severity: high
confidence_anchor: 75
discovered_by: t7-injection-specialist
status: active
---
```

## Grep-First Retrieval (before delegations)

1. Extract keywords from current target context
2. Grep YAML frontmatter (technology:, attack_type:, tags:)
3. Read frontmatter only of candidates (check status != stale, anchor >= 50)
4. Score: strong (3/3 match), moderate (2/3), weak (1/3 skip)
5. Full-read strong and moderate matches only (max 5 for injection)

## Contradiction Detection

When new learning contradicts existing:
- New anchor > old by 1+ level: Replace with note
- Same anchor level: Present both to operator
- Old anchor > new: Keep old, note discrepancy

## Staleness Detection

Flag stale at phase boundaries:
- Credential entries past known expiry
- Target services changed (new recon shows different versions)
- Architecture changes (new scan shows different infrastructure)
