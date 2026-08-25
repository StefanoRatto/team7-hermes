# Smart Escalation Table

When the 3-strike rule triggers reassessment, match the failure pattern before escalating. Do not guess — diagnose first.

## Failure Patterns

| Pattern | Diagnosis | Escalation Action |
|---------|-----------|-------------------|
| Same exploit fails on multiple different endpoints | Target-wide protection (WAF, IDS, rate limiting) | Delegate to t7-recon-agent: enumerate protections explicitly. Then try evasion from a different angle. |
| Exploit works in test but fails on target | Environment difference (OS, patches, config, containerization) | Delegate to t7-investigator-agent: compare environments. Fingerprint exact target version. |
| Auth bypass works then stops working | Adaptive defense, session invalidation, anomaly detection | Investigate timing pattern. Try from different source. Check for honeypot indicators. |
| Exploit succeeds but post-exploitation shows unexpected results | Wrong mental model of target architecture | Delegate to t7-recon-agent: deeper enumeration. Target is not what we thought. Update architecture learnings. |
| 3+ different vectors all fail on same component | Hardened component — well-defended or out of scope | Document hardening. Pivot to adjacent, less-defended components. Flag as "defended." |
| Tool crashes or produces garbage output | Tool incompatibility, anti-analysis measures | Try alternative tool. Check version compatibility. Delegate to t7-tools-arsenal-agent. Log as dead_end. |
| Fix/exploit works but prediction was wrong | Found symptom, not root cause — works by accident | Return to investigation. Understanding why prevents fragile reliance on coincidence. |
| Contradictory evidence from different agents | Wrong assumption shared by both, or non-deterministic target | Invoke Confusion Protocol. Do NOT average contradictions — investigate which is accurate. |

## Usage

After 3 strikes on the same vector:
1. Stop attempting
2. Match the failure pattern above
3. Follow the escalation action
4. Document the dead end in wisdom accumulation
5. Pivot to the recommended alternative
