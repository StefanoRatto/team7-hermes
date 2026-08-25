# Engagement Discovery Protocol

Run as Phase 0 before any testing begins. Interrogate scope with structured forcing questions. Push back on vague scoping. Challenge assumptions.

## The Six Security Forcing Questions

Run one at a time via t7-planner-agent. Push on each until the answer is specific, evidence-based, and uncomfortable.

### Question 1 — Threat Reality
"Who specifically would attack this system, and what would they gain? Name a real adversary profile, not a hypothetical. Script kiddie scanning for low-hanging fruit? Motivated insider with database access? Nation-state APT targeting IP? The answer changes everything about how we test."

### Question 2 — Status Quo
"What security controls already exist? What has already been tested? What did the last assessment find? If the last pentest found SQLi and it is still there, that tells us more than any new scan."

### Question 3 — Desperate Specificity
"Which single asset, if compromised, causes the most damage? Name it. Not 'the database' — which table, which column, which record. Not 'customer data' — whose data, how much, what is the regulatory exposure?"

### Question 4 — Narrowest Attack Surface
"If you could only test 3 endpoints or 3 services, which ones? Why those? If the answer is 'all of them,' push back — what keeps the CISO up at night?"

### Question 5 — Observation and Surprise
"What about this system's architecture surprised you or seems unusual? Where did the developers make non-obvious choices? The unusual parts are where the vulnerabilities hide."

### Question 6 — Scope Fit
"Given the time budget, what level of coverage is realistic? What gets cut first — depth or breadth? A shallow scan of everything, or a deep dive on the crown jewels? Name the tradeoff explicitly."

## Anti-Sycophancy Rules

- Never say "that's a reasonable scope" without evidence
- Take a position on every answer: "I would prioritize X over Y because..."
- If operator says "test everything," push back with effort estimates
- If operator downscopes aggressively, name blind spots being accepted
- Maximum 2 pushback attempts per question. If operator insists twice, accept and log.

## Premise Challenge

After gathering answers, present falsifiable claims:
```
Based on your answers, I am operating on these premises:
1. The primary threat is [adversary profile] motivated by [objective]
2. The crown jewel is [specific asset] with [regulatory exposure]
3. [System X] has not been tested since [date] and is highest priority
4. Time budget supports [depth/breadth tradeoff]

Do you agree with each premise? Disagreeing changes the engagement plan.
```

## Autoplan Pipeline

After Discovery, run the 4-phase Autoplan:

### Phase A: Strategic Review (t7-oracle-agent)
- Is scope realistic for time budget?
- High-value targets being ignored?
- Low-value targets consuming disproportionate time?
- Output: Prioritized target list with effort estimates

### Phase B: Technical Review (t7-recon-agent + t7-code-review-agent, parallel)
- Quick recon to validate assumptions
- Technology stack detection → skill pack selection
- White-box analysis if source available
- Output: Confirmed attack surface, technology inventory

### Phase C: Compliance Review (t7-compliance-agent, conditional)
- Only if engagement includes compliance requirements
- If compliance adds <30 min of agent time, include it
- Output: Compliance scope additions

### Phase C.5: Scope Document Review
Dispatch parallel review personas:
- COHERENCE REVIEWER: Scope boundaries consistent?
- FEASIBILITY REVIEWER: Achievable in time budget?
- THREAT MODEL REVIEWER: Missing obvious threat vectors?
- SCOPE GUARDIAN: Too broad for budget?
- ASSUMPTIONS CHALLENGER: Top 3 ways engagement could fail?

### Phase D: Scope Lock
- Present consolidated scope to operator
- Classify decisions: MECHANICAL (auto), TASTE (auto+surfaced), OPERATOR CHOICE (must ask)
- Only OPERATOR CHOICE items require human input
- Output: Locked engagement scope document

## Decision Principles

1. Choose completeness — test the whole attack surface if it is a "lake"
2. Boil lakes — adjacent trivially reachable system? Include it.
3. Pragmatic — similar coverage? Pick faster approach.
4. Explicit over clever — standard tools first.
5. Depth over breadth for critical assets.
6. Bias toward action — start testing after scope is "good enough."
