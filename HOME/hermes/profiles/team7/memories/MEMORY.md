Team7 profile ops: t7/* skills are user-owned — skill_manage cannot patch them by name; edit the SKILL.md files directly (hermes curator adopt can reclaim them). Doc-format skills (pdf/docx/pptx/xlsx under productivity/) run via /home/raste/.venvs/doc-tools/bin/python (pypdf, reportlab, pdfplumber, pypdfium2, python-docx, python-pptx, openpyxl).
§
Delegation lifecycle lesson (async completion notices reach only the orchestrator; relay + verify on disk before status claims; transcripts at cache/delegation/live/<id>/task-N.log): t7-orchestrator skill, Delegation Mechanics.
§
SOUL.md is Hermes-native and fits the 216K-char system-prompt cap (2026-10-19 rewrite + slim). Deep identity doctrine, expertise playbooks, agent routing, delegation templates: t7-orchestrator/references/. ~/.t7/team7.md is the STALE pre-rewrite opencode original — NEVER restore SOUL.md from it.
§
Memory policy: this file holds profile-ops facts only; engagement learnings go to deliverables/learnings/ (Compound Knowledge Store), procedures to skills. When full, consolidate oldest-first; only then consider raising memory_char_limit.
§
REA integrated (2026-10-20): t7-rea adapted skill (from morluto/rea reverse-engineer-anything v43, MIT; 6 references vendored verbatim inside the skill) + REA MCP server pinned rea-agents@6.7.0 in config.yaml mcp_servers (tools = mcp_rea_*; update = bump pin + re-vendor references). No analysis engines on host — native decompilation gap documented in the skill.
§
Web-RVRM integrated (2026-10-20): t7-web-rvrm skill = operator's own methodology (StefanoRatto/Web-RVRM v4.0) vendored lean as 10 byte-verified section references (deep-dives + Burp ext stay upstream as URLs); two usage modes (in-engagement lens + standalone research via /goal per phase); detection tiers map to t7 anchors; update = re-fetch + re-split (reassembly must stay byte-identical).
