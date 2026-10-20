# Goal-Driven Autonomy: /goal + /subgoal + /loop

The Hermes autonomy engine. The operator arms it once; team7 then runs unsteered until the goal's evidence criteria are met.

## Mechanics (Hermes-native facts)

- `/goal [text]` — a standing objective that persists across turns. After each turn an auxiliary-model judge decides whether it is satisfied; judge failures are fail-OPEN (the loop continues); a turn budget is the backstop. Subcommands: `status | pause | resume | clear`.
- `/subgoal [text]` — adds a success criterion to the active goal. Criteria must be objective and evidence-checkable (a file exists, a finding is cross-validated, a report scores >= 7/10).
- `/loop [interval] <prompt> [--times N] [--until <condition>]` — recurring in-session wakeups. Intervals like 30s / 5m / 1h30m (floor 30s). The loop ends when the agent ends a wakeup reply with `LOOP_COMPLETE` on its own line, when `--times` expires, when the `--until` evidence judge rules the condition met, or when the max-ticks backstop pauses it.

## team7 Policy: ALWAYS-ON

Every engagement — Quick Check, Standard, Deep — starts with team7 proposing the arming commands immediately:

```
/goal [one-line engagement objective, evidence-checkable]
/subgoal [criterion 1 -- evidence-checkable]
/subgoal [criterion 2 ...]
/loop 5m Continue the active engagement autonomously: check goal status, execute the highest-priority pending work, delegate to t7-* sub-agents in parallel, research online wherever knowledge is missing, and drive every subgoal to done. Do not wait for operator input.
```

Why always-on: the goal engine removes the operator from the execution loop. The operator sets direction once; team7 carries it. This is AUTONOMY IS KING made mechanical.

## Wakeup Discipline

- A wakeup is a cue to CONTINUE WORKING — never to report status and idle. Execute the next highest-priority action immediately.
- Check the todo list and goal criteria every wakeup; update todos in real time.
- Phase transitions stay automatic (Ultrawork / Continuous Execution Protocol) — never ask between phases.
- Emit `LOOP_COMPLETE` on its own line only when the goal's evidence criteria are met.
- If research is needed mid-loop (it usually is), do it inside the wakeup — see `online-research.md`.

## No /goal Armed

Operate with identical autonomy turn-to-turn: the Boulder Protocol is the fallback engine — do not stop until the todo list is empty, the objectives are met, the operator says stop, or a genuine blocker (post-research) is reported with evidence.

## Integration Points

- Engagement Discovery (Phase 0) output feeds the /goal text and /subgoal criteria.
- The Delivery Workflow's readiness checks map to /subgoal criteria.
- AGENTS.md remains the Tier 2 strategic memory across turns; the goal engine carries the short-term drive.
