---
name: t7-web-rvrm
description: "Web-RVRM: the operator's Web RFC-Based Vulnerability Research Methodology — specification-level web vulnerabilities: parser differentials, gateway desynchronizations, RFC ambiguities. Use for RFC-driven vuln research, differential testing across CDN/WAF/proxy stacks, hypothesis-card-driven web testing, and desync/smuggling investigations."
version: 1.0.0
author: team7
license: "Operator-owned methodology (StefanoRatto/Web-RVRM); vendored by the author into this private profile"
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [web-security, rfc-research, parser-differential, desync, vulnerability-research, methodology, offensive-security]
    category: t7
    related_skills: [t7-pentesterweb, t7-bbhunter, t7-ssrf-specialist, t7-injection-specialist, t7-report-generation-agent]
---

# t7-web-rvrm — Web RFC-Based Vulnerability Research Methodology

> The operator's own methodology (Web-RVRM v4.0 — https://github.com/StefanoRatto/Web-RVRM). The ten reference files in `references/` are EXACT section slices of the upstream Web-RVRM-Methodology.md (reassembly verified byte-identical); their content is authoritative. Deep-dives and the Burp extension stay upstream (URLs below).

## When to Use

Two usage modes:

1. **IN-ENGAGEMENT LENS** — during web assessments (t7-pentesterweb / t7-bbhunter work): when the target stack includes a CDN / WAF / reverse proxy / API gateway / runtime chain, apply RVRM's vectors and hypothesis framework to generate differential probes (CL.TE variants, hop-by-hop stripping, obs-fold, downgrade persistence) alongside standard OWASP testing. Tier-2/3 differentials found this way chain into the engagement's findings.
2. **STANDALONE RESEARCH** — full RVRM pipeline engagements (original vulnerability research, bug-bounty class discovery, article material, IETF errata): Phase 1 RFC corpus -> Phase 2 three-tier spec audit -> Phase 3 hypothesis cards -> Phase 4 differential testing -> chaining -> disclosure. Propose `/goal` with `/subgoal` criteria mapped to the phases (corpus scored / audit complete / hypotheses ranked / differential matrix run / report drafted); the goal engine drives it — no steering between phases.

Trigger surface: RFC-based vulnerability research, parser differential, request smuggling, desync, desynchronization, CL.TE, hop-by-hop, gateway, specification ambiguity, ABNF, normative language audit, RFC 2119, hypothesis card, differential testing, protocol downgrade, deprecated feature, error handling divergence, IETF errata, internet-draft, "why do these two servers disagree".

## Reference Routing (read only the relevant section)

| Task | Reference |
|---|---|
| Foundations: LangSec, Postel failure modes, formal divergence model, FSA, L* inference | `references/rvrm-foundations.md` |
| Phase 1: RFC target discovery, lineage graphs, Web-RPSF scoring, W0-W3 tier catalog | `references/rvrm-phase1-target-discovery.md` |
| Phase 2: three-tier spec audit (RFC 2119 normative language, ABNF grammar, deprecation drift), go/no-go matrix | `references/rvrm-phase2-spec-audit.md` |
| Phase 3: master worksheet, hypothesis derivation, Web Hypothesis Cards, Web-RARP priority | `references/rvrm-phase3-hypothesis-generation.md` |
| Phase 4: target matrix, differential pipeline, DRQ noise reduction, SPA race testing, detection tiers, safe/aggressive modes, tooling, t-way coverage | `references/rvrm-phase4-differential-testing.md` |
| The eight research vectors (downgrade persistence, deprecated features, parser differentials, version conflicts, state machine discrepancies, extension abuse, cross-protocol gateways, error divergence) | `references/rvrm-vectors.md` |
| Chaining and attack-graph synthesis, primitive taxonomy, severity amplification | `references/rvrm-chaining.md` |
| Disclosure: multi-party protocols, IETF errata lifecycle, Internet-Drafts, report templates, defensive hardening | `references/rvrm-disclosure.md` |
| Master templates: RFC analysis worksheet, hypothesis card, differential execution matrix, end-to-end operational checklist | `references/rvrm-templates.md` |
| Appendices: glossary, ABNF core syntax, automation scripts (async differential runner, SPA runner, ABNF mutator) | `references/rvrm-appendices.md` |

## t7 Integration

**Detection tier -> confidence anchor mapping** (RVRM §6.5 -> t7 anchors):

| Web-RVRM tier | t7 anchor | Handling |
|---|---|---|
| Tier 1: Confirmed (complete exploitation verified, before/trigger/after trace) | 75; 100 when independently reproduced by a second technique/agent | Report finding |
| Tier 2: High (differential confirmed across >= 2 implementations, exploitation modeled) | 50 | Report; refine payload |
| Tier 3: Medium (significant differential, logic restricts exploitation) | 50 + mandatory chaining investigation note | Investigate chaining (rvrm-chaining.md) |
| Tier 4: Low (anomalous error/timing, no state deviation) | 25 | Suppression gate applies: log as specification divergence in WISDOM/learnings, not a report finding |
| Passive Indicator (architectural pattern only) | 0-25 | Trigger targeted active checks |

**Safe/Aggressive mode**: Safe Mode (mathematically inert canary tokens, non-destructive mutations, rate-limit respect) is the DEFAULT for production and bounty work and aligns with the t7 exploitation risk budget. Aggressive Mode (functional exploit primitives, SPA floods, full impact proofs) requires explicit operator authorization (operator sovereignty) and laboratory/authorized scope.

**Traceability**: validated hypothesis cards become F-NNN findings with their differential matrices as E-NNN evidence; RVRM attack chains feed AP-NNN attack paths.

**Delegation**: sub-agents receive RVRM directives via the delegation CONTEXT (precision doctrine). Example: "Apply t7-web-rvrm Vector 3 (parser differentials): run the ABNF mutation set from the skill's rvrm-appendices.md against the target pair; return DRQ distances + raw traces + tier classification." The methodology references are readable by any sub-agent via this skill's path.

## Upstream Pointers (not vendored — lean skill)

- Worked deep-dives (templates for new RFC dives): https://github.com/StefanoRatto/Web-RVRM/tree/main/web-RFCs-Deep-Dives (RFC 5789 HTTP PATCH, RFC 6265 Cookies)
- Burp extension (rfc5789-auditor, Java/Gradle): https://github.com/StefanoRatto/Web-RVRM/tree/main/SW/Burp_Extensions/rfc5789-auditor — buildable software; no Burp on this host; usable when an operator-supplied Burp environment exists.

## Operational Notes

- The automation scripts in `references/rvrm-appendices.md` are runnable Python (differential runner, SPA runner, ABNF mutator) — copy them into the engagement workspace before executing; never edit the reference in place.
- Follow the End-to-End Web Research Operational Checklist (`references/rvrm-templates.md`) for standalone research engagements.
- Online research protocol applies: RFC texts, errata, and HTTPWG drafts are primary sources — fetch and read the actual RFC; never work from memory of it.

## Maintenance

Upstream is the operator's repo. To update: re-fetch Web-RVRM-Methodology.md, re-run the section split (reassembly must remain byte-identical), and re-diff this wrapper's tier mapping against §6.5.
