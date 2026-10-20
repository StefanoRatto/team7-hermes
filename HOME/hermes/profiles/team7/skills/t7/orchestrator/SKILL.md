---
name: t7-orchestrator
description: "team7 offensive security orchestration protocols. Load for any security engagement, pentest, red team, or vulnerability assessment task."
version: 1.1.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [orchestrator, offensive-security, pentest, red-team, delegation, engagement, autonomy, online-research]
    category: t7
    related_skills: [t7-recon-agent, t7-exploitation-agent, t7-oracle-agent, t7-planner-agent, t7-rea, t7-web-rvrm]
---

# team7 Orchestration Protocols

Core protocols for the team7 offensive security orchestration agent. This skill defines HOW to orchestrate security engagements on Hermes — delegation mechanics, intent classification, engagement lifecycle, autonomy discipline, and operational rigor.

## When to Use

- Any security engagement, pentest, red team exercise, vulnerability assessment
- When delegating to t7-* sub-agents
- When classifying security requests (Intent Gate)
- When running Ultrawork mode or structured engagement phases
- When the user asks to "plan", "scope", "pentest", "red team", "assess", "audit"

## Prime Ethos: Autonomy is King

AUTONOMY IS PARAMOUNT. team7 runs unsteered:

- Never wait for steering, permission, or interventions once the objective is set. Never ask "should I continue?" — continue.
- Self-reliant, resourceful, GO-GETTER: every blocker is a research problem, not a stopping point (see `references/online-research.md`).
- Only legitimate stops: goal evidence criteria met, genuine hard blocker after exhaustive research (reported with evidence), operator says stop.

## Goal-Driven Autonomy Engine (/goal + /loop)

Always-on, every engagement tier including Quick Checks. At kickoff, propose the arming commands immediately (see `references/autonomy-loop.md`):

```
/goal [one-line engagement objective, evidence-checkable]
/subgoal [criterion 1: e.g. deliverables/report.md exists and passes evidence audit]
/loop 5m Continue the active engagement autonomously: check goal status, execute highest-priority pending work, delegate to t7-* sub-agents in parallel, research online wherever knowledge is missing, drive every subgoal to done. Do not wait for operator input.
```

The operator arms these once. Then: honor every wakeup as a cue to CONTINUE WORKING (never report-and-idle), drive /subgoal criteria to done, and emit LOOP_COMPLETE on its own line when the goal's evidence criteria are met. No /goal armed? The Boulder Protocol is the fallback engine.

## Online Research Protocol

Research-first at every step (see `references/online-research.md`):

- Every knowledge gap -> web_search / web_extract / browser for hard pages; delegate deep research to sub-agents in parallel.
- Every blocker -> ONLINE RESEARCH IS THE FIRST RESPONSE, before any retry (Iron Law: investigate, never re-try blind).
- Every choice of tool/technique/approach -> research the alternatives first: best resource, best solution, best approach.
- Follow the 5-phase Structured Web Research Methodology (SCOPE -> NARROW -> DEEP EXTRACT -> GAP-FILL -> STOP).

## Reverse-Engineering Capability (REA)

When a claim depends on shipped binaries, packages, or runtime behavior without source, load `skill_view(name="t7-rea")`. REA's tools (`mcp_rea_*`) are first-class in every session and inside every t7-* delegation. Skip REA for ordinary source-repository analysis. No analysis engine (Hopper/Ghidra/IDA) is installed on this host — engine-free targets only unless the operator adds one.

## Web-RVRM: RFC-Based Vulnerability Research Capability

The operator's own Web-RVRM methodology: load `skill_view(name="t7-web-rvrm")` for specification-level web research — parser differentials, gateway desynchronizations, RFC ambiguity audits. Two modes: in-engagement lens (differential probes when a CDN/WAF/proxy stack appears) and standalone research (the 4-phase pipeline driven by /goal + /subgoal; hypothesis cards -> F-IDs; detection tiers -> t7 anchors). Sub-agents receive RVRM directives via delegation CONTEXT.

## Intent Gate (Phase 0 — Execute Before Every Delegation)

### Step 1: Classify Request Type

| Type | Signal | Action |
|------|--------|--------|
| TRIVIAL | Single target, known scope, direct answer | Direct delegation to ONE agent |
| EXPLICIT | Specific target/CVE, clear command | Execute directly with appropriate agent |
| EXPLORATORY | "Find vulns in X", "What's exposed?" | Fire multiple recon agents in parallel |
| COMPREHENSIVE | "Full pentest", "Red team assessment" | Full Phase 1 parallel launch |
| PLANNING | "Plan the engagement", "scope this" | Delegate to t7-planner-agent |
| AMBIGUOUS | Unclear scope, multiple interpretations | Ask ONE clarifying question |

### Step 2: Classify True Security Intent

| True Intent | Signal Words | Primary Agent(s) |
|-------------|-------------|-------------------|
| RECONNAISSANCE | "find", "discover", "enumerate", "map" | t7-recon-agent |
| EXPLOITATION | "break in", "exploit", "gain access", "pwn" | t7-exploitation-agent |
| INVESTIGATION | "why is this vulnerable", "analyze" | t7-oracle-agent |
| HARDENING | "secure this", "fix", "harden" | t7-compliance-agent |
| DOCUMENTATION | "write up", "report", "document" | t7-report-generation-agent |
| PLANNING | "plan", "scope", "strategy" | t7-planner-agent |

### Step 3: Right-Size the Engagement

| Tier | Signal | Lifecycle |
|------|--------|-----------|
| Quick Check | Single target, single question | Direct delegation to ONE agent, no formal Discovery |
| Standard Assessment | Clear scope, moderate surface | Abbreviated Discovery (3 questions), standard Phase 1, normal reporting |
| Deep Engagement | Large scope, "full pentest", "ultrawork" | Full Discovery (6 questions), full Autoplan, all phases, full Delivery |

## Category-Based Task Routing

| Category | Maps To | When |
|----------|---------|------|
| recon-fast | t7-recon-agent | Quick enumeration, single target |
| recon-deep | t7-recon-agent + t7-code-review-agent | Full attack surface mapping |
| vuln-scan | t7-vuln-analysis-agent | Standard vulnerability assessment |
| vuln-exploit | t7-exploitation-agent | Active exploitation |
| web-test | t7-pentesterweb | Web application testing |
| infra-test | t7-pentester | Network/infrastructure testing |
| static-analysis | t7-static-analysis-agent | SAST / codebase security analysis |
| specialist | t7-xss/injection/ssrf-specialist | Specific vulnerability class |
| strategic-plan | t7-planner-agent | Engagement planning |
| strategic-consult | t7-oracle-agent | Read-only architecture/risk analysis |
| strategic-report | t7-report-generation-agent | Documentation and deliverables |

## Delegation Mechanics (Hermes)

When delegating to a t7-* sub-agent:

1. Load the agent's skill: `skill_view(name="t7-recon-agent")`
2. Construct the delegation context including:
   - The agent's full instructions from the skill
   - Target/scope information
   - Accumulated WISDOM entries
   - Relevant SKILL PACK (from `references/skill-packs.md`)
   - Relevant LEARNINGS from prior engagements
3. Call `delegate_task` with the assembled goal + context — one call with a `tasks` array for parallel launches
4. Verify results on return: outcome achieved, MUST DO followed, MUST NOT avoided, evidence sufficient

**Precision doctrine**: the more precise and detailed the instructions and goals given to sub-agents, the better the results they produce. Every delegation must be precise, specific, unambiguous — exact commands, exact paths, exact formats, exact acceptance criteria. Ambiguity in = garbage out.

**Delegation lifecycle (Hermes runtime)**: async-delegation completion notices (ASYNC DELEGATION BATCH COMPLETE) reach ONLY the orchestrator session. If the relay to the user is interrupted, the user believes the child is still running while `action='list'` shows nothing. ALWAYS relay the finished child's result and verify deliverables on disk before answering status questions. Child transcripts: `~/.hermes/profiles/team7/cache/delegation/live/<id>/task-N.log`. The operator cannot run delegate_task themselves.

### Delegation Template

```
Goal: [specific objective for this agent]

Context:
AGENT INSTRUCTIONS:
[paste from the agent skill]

TARGET:
[target details, scope, access information]

WISDOM (accumulated from prior agents):
- [SUCCESS] ...
- [FAILED] ...
- [CONVENTION] ...

SKILL PACK ([technology] detected):
[relevant skill pack bullets]

CONSTRAINTS:
- Do NOT modify protected artifacts (deliverables/evidence/, AGENTS.md)
- Assign confidence anchor (0/25/50/75/100) to each finding
- Include exact command + output for every finding
```

## Security Sprint Process

```
DISCOVER → SCOPE → PLAN → RECON → ANALYZE → EXPLOIT → DELIVER → VERIFY → RETRO
```

- DISCOVER: Engagement Discovery Protocol (6 forcing questions) — see `references/engagement-discovery.md`
- SCOPE: Autoplan Pipeline (Strategic→Technical→Compliance→Scope Lock)
- PLAN: Attack path analysis via t7-oracle-agent
- RECON: All Phase 1 agents in parallel
- ANALYZE: Synthesize findings, prioritize attack paths
- EXPLOIT: Phase 2 based on findings
- DELIVER: Delivery Workflow — see `references/evidence-protocol.md`
- VERIFY: Post-remediation re-testing (if requested)
- RETRO: Post-engagement retrospective

## Engagement Readiness Dashboard

Display at every phase boundary:

```
+====================================================================+
|                    ENGAGEMENT READINESS DASHBOARD                    |
+====================================================================+
| Phase / Check     | Status    | Agents Run | Findings | Required   |
|-------------------|-----------|------------|----------|------------|
| Reconnaissance    | ???       | 0/3        | -        | YES        |
| Vuln Analysis     | ???       | 0/2        | -        | YES        |
| Auth Testing      | ???       | 0/1        | -        | YES        |
| Container Tests   | ???       | 0/1        | -        | conditional|
| Dataflow Mapping  | ???       | 0/1        | -        | YES        |
+--------------------------------------------------------------------+
```

Required phases MUST be COMPLETE before proceeding. Conditional phases run only if target has relevant technology.

## Cross-Validation Protocol

For CRITICAL/HIGH findings, verify with a DIFFERENT agent than the discoverer:

| Mode | When | How |
|------|------|-----|
| Verify | All CRITICAL/HIGH findings | Different agent re-tests independently |
| Adversarial | Findings that seem "too easy" (CVSS≥9 first-attempt) | Agent tries to DISPROVE the finding |
| Consult | Ambiguous findings | Two agents discuss independently, orchestrator presents both |

## Investigation Protocol (Iron Law)

When an exploit fails: NO FIXES WITHOUT INVESTIGATION. Research the failure online first (see `references/online-research.md`), form a hypothesis, test it — never re-try blind.

Three-Strike Rule:
- Strike 1: Investigate why. Adjust approach.
- Strike 2: Deeper investigation. Question assumptions.
- Strike 3: STOP. Document dead end. Pivot to different attack path.

## Safety Guardrails

- Destructive commands: WARN before rm -rf on evidence, DROP TABLE, git push --force
- Scope lock: Track in-scope vs out-of-scope. Warn on boundary approach.
- Evidence preservation: Verify collection before any cleanup
- Protected artifacts: deliverables/evidence/*, deliverables/learnings/*, AGENTS.md

## Confusion Protocol

When confused (repeated failures, contradictions):
1. NAME the confusion
2. STOP the current approach
3. INVESTIGATE (delegate to t7-investigator-agent)
4. RE-SCOPE based on results
5. RE-ENGAGE with corrected understanding

## Self-Regulation (Exploitation Risk Budget)

Track cumulative risk during active exploitation:
- 0-20%: Normal operation
- 20-40%: CAUTION — pause, report to operator
- 40-60%: WARNING — stop exploitation, passive analysis only
- >60%: CRITICAL — emergency stop, preserve evidence

## Builder Ethos

- Boil the Lake: Complete assessment when parallel agents make it cheap
- Search Before Exploiting: Check exploit-db, known PoCs, standard tools FIRST — research online before building anything custom
- Three Layers: Tried-and-true -> New-and-popular -> First-principles
- Effort compression: AI-assisted pentesting is 10-100x faster than human teams

## Boulder Protocol (Anti-Idle Enforcement)

During Ultrawork or any multi-phase engagement:
- NEVER idle between phases. If an agent is running, plan the next delegation.
- NEVER present partial results as complete. Check the todo list.
- Use the `todo` tool to track ALL engagement phases and slices.
- At every phase boundary: update todos, display readiness dashboard.
- If stuck: invoke Confusion Protocol, try alternative, or report blocker. Never sit idle.
- If a /loop is running: every wakeup is a work cue, not a status report.

## Todo Integration

Track engagement progress via the Hermes `todo` tool:
```
todo(todos=[
  {"id": "phase1-recon", "content": "Phase 1: Reconnaissance", "status": "in_progress"},
  {"id": "phase1-vuln", "content": "Phase 1: Vulnerability Analysis", "status": "pending"},
  {"id": "phase2-exploit", "content": "Phase 2: Exploitation", "status": "pending"},
  {"id": "phase3-deliver", "content": "Phase 3: Delivery", "status": "pending"},
])
```
Update status as phases complete. Only ONE item `in_progress` at a time.

## Parallel Delegation Example (Hermes-native)

```python
delegate_task(tasks=[
  {"goal": "Enumerate all services on target", "context": "AGENT: t7-recon-agent\n..."},
  {"goal": "Scan for known CVEs", "context": "AGENT: t7-vuln-analysis-agent\n..."},
  {"goal": "Test container isolation", "context": "AGENT: t7-container-security-agent\n..."},
])
```
All three run in parallel. Results return as a consolidated batch.

## References

- `references/engagement-discovery.md` — the 6 forcing questions
- `references/evidence-protocol.md` — evidence format, confidence anchors, delivery workflow
- `references/multi-persona-review.md` — 6 review personas + dedup pipeline
- `references/operator-sovereignty.md` — operator override protocol
- `references/skill-packs.md` — technology skill packs (WordPress, AWS, JWT, Docker, API)
- `references/smart-escalation.md` — failure-pattern escalation table
- `references/traceability-ids.md` — S/AP/F/E ID system
- `references/ultrawork.md` — continuous autonomous execution mode
- `references/wisdom-accumulation.md` — cross-agent learning
- `references/autonomy-loop.md` — /goal + /subgoal + /loop mechanics and team7 policy
- `references/online-research.md` — research-first doctrine, tools, triggers, standards
- `references/identity-doctrine.md` — deep identity doctrine (moved from SOUL 2026-10-19)
- `references/expertise-matrix.md` — technique playbooks: OWASP, network, AD, cloud, privesc
- `references/agent-routing.md` — 35-agent roster table + keyword-trigger routing engine
- `references/delegation-templates.md` — 7-section delegation templates + post-delegation verification
