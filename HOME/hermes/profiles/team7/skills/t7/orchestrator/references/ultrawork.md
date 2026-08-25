# Ultrawork Mode

Trigger keywords: `ultrawork`, `ulw`, `full assessment`, `full pentest`, `go full auto`

## Behavior

When activated, enter continuous autonomous execution:

1. Run Engagement Discovery (6 forcing questions) if no scope defined
2. Run Autoplan Pipeline (Strategic→Technical→Compliance→Scope Lock)
3. Launch ALL Phase 1 agents in parallel immediately
4. Synthesize Phase 1 results + run Documentation Sync
5. Automatically chain to Phase 2 based on findings
6. Continue to Phase 3
7. Run Delivery Workflow (evidence audit + report + packaging)
8. Write security posture baseline for future comparison
9. Generate retrospective
10. Do NOT stop until all phases complete or user interrupts

## Rules

- Phase transitions are AUTOMATIC — no user confirmation needed between phases
- Findings from Phase 1 feed directly into Phase 2 delegation prompts
- Wisdom accumulation is active throughout
- Todo list tracks all phases, updated in real-time
- The final deliverable is a complete report package, not a status update
- To stop: User says "stop", "pause", or "hold"

## Sprint Mapping

| Sprint Phase | Ultrawork Step |
|-------------|----------------|
| DISCOVER | Step 1 |
| SCOPE | Step 2 |
| PLAN+RECON | Step 3 |
| ANALYZE | Step 4 |
| EXPLOIT | Steps 5-6 |
| DELIVER | Step 7 |
| Baseline write | Step 8 |
| RETRO | Step 9 |

## Continuous Execution Loop

```
LOOP:
  1. ASSESS: Check engagement objective status
     → All objectives COMPLETE? → EXIT, generate report
     → Objectives remaining? → CONTINUE

  2. PLAN: Identify highest-priority incomplete objective
     → What agents needed? What wisdom applies? What failed before?

  3. EXECUTE: Delegate to appropriate agents (parallel where possible)
     → Include accumulated wisdom in delegation

  4. SYNTHESIZE: Collect results, extract wisdom
     → New attack paths? Add to plan. Blocked? Alternative approach.

  5. GOTO 1
```

## Anti-Premature-Stop Rules

- Do NOT exit because "enough findings were found"
- Do NOT exit because one phase is complete (continue to next)
- Do NOT ask "should I continue?" — check objective status instead
- DO exit if genuinely blocked on all fronts (report blockers clearly)

## Checkpoints

Write a continuous checkpoint at every phase boundary to `deliverables/checkpoint-latest.md`:
- Current phase and step
- Completed objectives
- Pending objectives
- Active/completed sub-agents
- Accumulated wisdom summary
- Risk budget status
