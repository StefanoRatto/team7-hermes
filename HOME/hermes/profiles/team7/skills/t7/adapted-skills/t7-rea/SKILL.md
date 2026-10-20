---
name: t7-rea
description: "Reverse Engineer Anything (REA) — reverse engineer shipped binaries, packages, apps, and runtime behavior via the REA MCP server. Use when a claim depends on shipped artifacts, decompilation, passive runtime evidence, or version comparison without source. Skip for ordinary source-repository analysis."
version: 1.0.0
author: team7
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [reverse-engineering, binary-analysis, decompilation, rea, offensive-security, evidence]
    category: t7
    related_skills: [t7-malwareanalyst, t7-exploitation-agent, t7-static-analysis-agent, t7-smart-contract-auditor]
---

# t7-rea — Reverse Engineer Anything (REA)

> Adapted from morluto/rea `.agents/skills/reverse-engineer-anything` (skill v43, rea-agents 6.7.0, MIT — https://github.com/morluto/rea).
> This SKILL.md is re-authored with team7 conventions. The six operational reference guides in `references/` are vendored VERBATIM from upstream — their tool names, arguments, and limits are authoritative.

## When to Use

Use REA when a claim depends on a shipped binary or package, decompilation, passive application runtime evidence, or comparison with behavior not established by available source. For ordinary analysis of a complete source repository, use normal repository tools and do NOT run REA readiness or provider commands.

Trigger surface: reverse engineer, decompile, disassemble, shipped binary, ASAR, Electron app internals, APK, IPA, managed assembly, Go/Rust binary metadata, IL2CPP, Godot PCK, WASM artifact, EVM bytecode, firmware image, SQLite snapshot, ELF layout, recorded crash, packet capture, "how does this app do X without source".

## How REA Is Wired into team7 (Hermes)

- The REA MCP server is registered in the team7 profile config (`mcp_servers.rea`, stdio: `npx -y rea-agents@6.7.0 mcp`). Its tools register as first-class `mcp_rea_*` tools in every team7 session AND inside every t7-* sub-agent delegation (sub-agents inherit the toolset).
- This skill supplies the investigation methodology; it does not add tools. If `mcp_rea_*` tools are available, proceed directly to the target — do NOT run diagnostics or setup before every investigation.
- CLI fallback (same workflows, no MCP needed): `npx -y rea-agents@6.7.0 <command> --json`. Example: `npx -y rea-agents@6.7.0 analyze-javascript-application /absolute/path/to/app --json`. Read the returned Evidence, limitations, and unknowns with the same care as an MCP result.
- If tools are absent, a call is blocked, or a required provider is unavailable: read `references/connection-and-recovery.md`. Diagnose the observed blocker before changing anything — client approval, PATH, registration, and engine availability have different remedies. Hermes is NOT a REA-supported setup client (registration here is manual stdio); never run `rea-agents setup` expecting Hermes detection.

## Engine Reality on This Host (known limitation)

Native binary decompilation (pseudocode/assembly) requires Hopper, Ghidra, or IDA — none installed. Engine-free capabilities that work today: static JavaScript/Electron (ASAR) analysis, Go binaries, Rust binaries (cargo-auditable), IL2CPP metadata (v29/31/39), Godot PCK, WASM (caller-supplied WABT 1.0.42), EVM bytecode, SQLite snapshots, ELF layout (caller-supplied pwntools), PE resources, recorded crashes, packet captures (caller-supplied TShark).

Readiness check when in doubt: `npx -y rea-agents@6.7.0 doctor --json`. Installing an engine is an operator decision (operator sovereignty) — when the gap blocks a question, document it as a limitation in findings instead of silently narrowing scope.

## Route the Target First

Choose the first tool from the target supplied. `open_binary` binds any archive/package or native target; target-free tools take their own explicit path.

| Target | First tool |
|---|---|
| ASAR / extracted JS/Electron tree | `analyze_javascript_application` |
| ZIP/APK/IPA/MSIX/DMG archive inventory | `open_binary` (then `inspect_artifact` when the graph helps) |
| Android APK code/classes/refs | `inspect_android_package` |
| Managed PE/CLI assembly | `inspect_managed_artifact` |
| Go binary metadata | `inspect_go_binary` |
| Rust binary (cargo-auditable) | `inspect_rust_binary` |
| Unity IL2CPP global-metadata.dat | `inspect_il2cpp_metadata` |
| WASM artifact | `inspect_wasm_artifact` |
| EVM bytecode carrier | `inspect_evm_interface` |
| SQLite snapshot | `inspect_sqlite_database` |
| Linux ELF layout/symbols | `inspect_binary_layout` |
| PE resource trees | `inspect_pe_resources` |
| Browser page (user-owned, already open) | `list_browser_targets` |
| Browser action required (click/type/navigate) | `capture_browser_scenario` |
| Electron runtime (already open) | `list_electron_targets` (check `inspect_electron_fuses` first) |
| HAR/SAZ/mitmproxy/qlog captures | `inspect_web_network_capture` |
| Recorded packet captures | `inspect_packet_capture` / `inspect_pcap_capture` |
| Native executable/library | `open_binary` (ENGINE REQUIRED — see limitation above) |

Full routing detail (firmware, signal recordings, Kuna/IDA providers, session availability): the verbatim upstream sections in this skill's references and the guides under https://github.com/morluto/rea/tree/main/docs.

## Work Summary-First (operational discipline)

- Start with the smallest useful overview: `analyze_javascript_application` / `inspect_binary_layout` with `"detail": "summary"`, then drill via `inspect_analysis_view` with the returned `parent_evidence_id`.
- Do not repeat an identical analysis or rejected call without a changed input or resolved blocker (Iron Law: investigate, never re-try blind).
- If MCP reports `resource_constraint` with `details.resource: "transport"`: switch to `inspect_analysis_view` on the same-session Evidence reference, `trace_application_feature` once a seed is known, or `export_evidence_bundle` to a selected path.
- If a target is ambiguous ("my app" with no path/name), ask which app — never choose an example target on the user's behalf.
- Never imply static analysis observed execution. Distinguish observations, inferences, and unknowns in every conclusion.

## t7 Evidence Integration

- REA returns Evidence IDs for every fact. CITE them. Map to t7 confidence anchors: direct REA Evidence = 50 (observed); corroborated by an independent technique or second tool = 75; two independent confirmations = 100. Findings without Evidence IDs = anchor 25 at most.
- Keep a finding ledger (question -> Evidence ID -> anchor -> remaining unknowns); mirror it into AGENTS.md FINDINGS at strategic checkpoints. REA Evidence IDs serve as the E-NNN traceability evidence.
- Do not describe a broad investigation as complete while required questions remain open — mark each answered / partially answered / unresolved (this is the REA suppression gate: anchor below threshold = not a finding).

## Orchestrator Delegation Pattern

REA work can run inside any t7-* delegation (tools are inherited). Precision doctrine applies: give sub-agents the exact tool, exact target path, exact question, and the Evidence IDs of prior findings to reuse. Example delegation context line: "REA tools available as mcp_rea_*; open_binary the target, analyze_javascript_application with detail summary first; return findings with Evidence IDs + confidence anchors."

For multi-question investigations, decompose into independent questions and run parallel delegations with distinct scopes pointed at the same prior Evidence (upstream staged workflow, step 6).

## Read Only the Relevant Guide

- `references/native-and-artifacts.md` — native binaries, managed assemblies, archives, extraction
- `references/javascript-applications.md` — ASARs, extracted JavaScript, feature tracing, version comparison
- `references/android-applications.md` — APK declarations, classes, methods, static references
- `references/runtime-observation.md` — browser/Electron observation, static/runtime reconciliation
- `references/evidence-workflows.md` — Evidence paging, comparisons, residual unknowns, verification
- `references/connection-and-recovery.md` — tool absence, blocked calls, provider unavailability, repair

## Maintenance

- Version-pinned: `rea-agents@6.7.0` (in config.yaml and this skill). To update: `npx -y rea-agents@latest doctor --json`, bump the pin in config.yaml `mcp_servers.rea.args`, and re-vendor the references from upstream `.agents/skills/reverse-engineer-anything/` (byte-diff against the current vendored copies).
- Finish every investigation: close opened native sessions with `close_binary`; explain findings in plain language tied to Evidence IDs.
