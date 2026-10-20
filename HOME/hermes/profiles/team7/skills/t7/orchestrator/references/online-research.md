# Online Research Protocol — Research-First at Every Step

Advanced online research is a core operating behavior, not a fallback. At every step requiring knowledge beyond current context, and at EVERY opportunity to improve an outcome, go online and learn. INDEPENDENT, RESOURCEFUL, GO-GETTER: the best resource possible, the best solution possible to a problem, the best approach possible to a blocker is ONLINE RESEARCH AND LEARNING NEW THINGS.

## Tools (Hermes-native)

| Tool | Use |
|------|-----|
| web_search | Broad discovery queries (SCOPE / NARROW phases) |
| web_extract | Full-page extraction: advisories, PoC repos, docs |
| browser (browser_exec) | Pages that defeat plain extraction: bot walls, JS-heavy apps — drive a real browser |
| delegate_task | Deep research delegated to sub-agents in parallel — precise research questions in, sourced answers out |
| ~/.t7/raste-notes.ctb.md | Targeted section reads for known-good sources (News and Research, Internet Monitoring, Penetration Testing library) — never load the whole 186KB file |

## Triggers

| Trigger | Action |
|---------|--------|
| Unknown technology / version / CVE | Research advisories, PoCs, writeups |
| Choosing tool, technique, approach | Research alternatives FIRST — best resource, best solution, best approach |
| ANY blocker, error, failed attempt | Research is the FIRST response: exact error, exact technology+version, exact technique — learn the root cause and known fixes before retrying |
| Unfamiliar output / protocol / encoding | Research it — never guess |
| Exploit / PoC development | exploit-db, GitHub PoCs, vendor advisories first (Search Before Exploiting) |
| Report writing | Research the target stack so remediation is copy-pasteable and version-exact |
| Delegation prompt construction | Research the target's tech so skill packs and instructions are precise — precision in, quality out |

## Blocker Escalation

1. STOP the approach (Investigation Protocol Iron Law — no blind retries)
2. RESEARCH ONLINE IMMEDIATELY: someone has hit this before — learn from them
3. Hypothesis from research -> test -> confirm/reject
4. Only after research exhausted: Smart Escalation Table -> fallback chains -> report the genuine blocker with evidence

## Standards

- 5-phase Structured Web Research Methodology (SCOPE -> NARROW -> DEEP EXTRACT -> GAP-FILL -> STOP HEURISTIC) for any non-trivial research task
- Convergence across independent sources is signal; one blog echoed ten times is one source
- Cite the source URL when research changed a decision
- Primary sources over aggregators: vendor advisories, PoC code, conference talks, official documentation
- Extract learnings that change operations into WISDOM + the Compound Knowledge Store (pattern / technique / dead_end / pitfall)
