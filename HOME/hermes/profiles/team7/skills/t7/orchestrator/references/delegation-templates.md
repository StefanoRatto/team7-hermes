# team7 Delegation Prompt Templates

Moved verbatim from SOUL.md on 2026-10-19 (slim-down: Hermes keeps only the first 168,000 + last 48,000 chars of SOUL.md in the system prompt; this block lived in the omitted middle). Load via skill_view(name="t7-orchestrator") or read directly when needed.

---

## OPTIMIZED PROMPT TEMPLATES FOR SUB-AGENTS

### MANDATORY DELEGATION STRUCTURE (ALL 7 SECTIONS REQUIRED)

**CRITICAL**: Every delegation prompt MUST include ALL 7 sections. Vague prompts produce poor results.

```
1. MISSION: [Atomic, specific objective - ONE action per delegation]
   - What exactly needs to be accomplished?
   - Single, clear goal (not multiple objectives)

2. TARGET: [Exact target identifier]
   - IP address, URL, domain, system name
   - Scope boundaries (what's in/out of scope)

3. EXPECTED OUTCOME: [Concrete deliverables with success criteria]
   - What specific output do you expect?
   - How will you know the task succeeded?
   - Format requirements for the response

4. REQUIRED TOOLS: [Explicit tool whitelist]
   - Which tools should the agent use?
   - Prevents tool sprawl and focuses execution

5. MUST DO: [Exhaustive requirements - leave NOTHING implicit]
   - Specific actions that MUST be performed
   - Quality standards to meet
   - Evidence requirements

6. MUST NOT DO: [Forbidden actions - anticipate and block rogue behavior]
   - Actions that are explicitly prohibited
   - Scope boundaries not to cross
   - Dangerous operations to avoid

7. CONTEXT: [Prior findings, constraints, integration points]
   - Results from previous agents
   - Environmental constraints
   - How this task fits into the larger engagement
```

### POST-DELEGATION VERIFICATION (MANDATORY)

After EVERY agent returns results, you MUST verify:

| Checkpoint | Question | Action if Failed |
|------------|----------|------------------|
| Outcome | Did it achieve the EXPECTED OUTCOME? | Re-delegate with clearer instructions |
| MUST DO | Did it follow all MUST DO requirements? | Request completion of missing items |
| MUST NOT | Did it avoid all MUST NOT DO violations? | Flag the violation, assess impact |
| Evidence | Is the evidence sufficient for next phase? | Request additional evidence |
| Quality | Is the output actionable and clear? | Request clarification or reformatting |

**NO VERIFICATION = INCOMPLETE TASK. NEVER skip this step.**

### CONTEXT-ADAPTIVE DELEGATION

Adapt your delegation style based on context pressure (see Adaptive Context & Session Continuity directive):

```
NORMAL CONTEXT (no compaction has occurred):
  -> Full 7-section delegation prompts
  -> Include relevant prior findings inline in CONTEXT section
  -> Standard verbosity -- be thorough

AFTER FIRST COMPACTION:
  -> Abbreviated prompts: MISSION + TARGET + EXPECTED OUTCOME + CONTEXT
  -> In CONTEXT section, write: "Read AGENTS.md for full engagement context"
  -> Do NOT repeat findings inline -- reference AGENTS.md or file paths
  -> Delegate MORE aggressively -- sub-agents have fresh context windows
  -> Instruct sub-agents to write detailed results to files:
     "Write full results to deliverables/<topic>.md, return 5-line summary"

AFTER SECOND COMPACTION (session rotation imminent):
  -> Minimum viable prompts: MISSION + TARGET + EXPECTED OUTCOME
  -> Complete only the current task, do not start new tasks
  -> Instruct all sub-agents to write to files
  -> Prepare for session rotation immediately after current task
```

**WHY THIS MATTERS**: Sub-agents get their own fresh context windows. When your orchestrator context is tight, pushing work to sub-agents is the most efficient use of total available context across the system.

---

### t7-recon-agent Prompt Template
```
MISSION: [Specific reconnaissance objective]
TARGET: [Target identifier - IP, domain, organization]
SCOPE: [In-scope assets and boundaries]
DEPTH: [quick | standard | comprehensive | exhaustive]

REQUIRED OUTPUTS:
- Asset inventory (IPs, domains, subdomains)
- Service enumeration (ports, versions, banners)
- Technology stack identification
- Potential attack surface areas
- Recommended next steps

CONSTRAINTS:
- [Any specific limitations]
- [Stealth requirements if applicable]

PRIORITY FOCUS:
- [Specific areas of interest]
```

### t7-vuln-analysis-agent Prompt Template
```
MISSION: [Vulnerability assessment objective]
TARGET: [Target systems/applications]
SCOPE: [Assessment boundaries]
CONTEXT: [Any prior reconnaissance data]

REQUIRED OUTPUTS:
- Vulnerability inventory with CVE references
- CVSS scores and severity ratings
- Exploitability assessment
- Business impact analysis
- Remediation recommendations prioritized by risk

SCAN INTENSITY: [passive | light | standard | aggressive]
FOCUS AREAS: [Specific vulnerability classes to prioritize]
```

### t7-container-security-agent Prompt Template
```
MISSION: [Container security objective]
TARGET: [Container/orchestration environment]
TEST CASES: [Specific TC-00X to execute]

REQUIRED OUTPUTS:
- Container isolation status
- Escape vector analysis
- Privilege assessment
- Resource limit verification
- Image security findings
- Compliance status (if applicable)

ENVIRONMENT: [Docker | Kubernetes | containerd | other]
ACCESS LEVEL: [Current access/privileges]
```

### t7-auth-bypass-agent Prompt Template
```
MISSION: [Authentication testing objective]
TARGET: [Authentication endpoint/system]
AUTH TYPE: [Basic | Form | OAuth | SAML | JWT | MFA | other]

REQUIRED OUTPUTS:
- Authentication mechanism analysis
- Bypass vectors identified
- Credential exposure findings
- Session management weaknesses
- Account enumeration results
- Lockout policy analysis (TC-008, TC-009 if applicable)

CREDENTIALS: [Any known credentials]
WORDLISTS: [Specific wordlists to use]
INTENSITY: [passive | moderate | aggressive]
```

### t7-dataflow-mapping-agent Prompt Template
```
MISSION: [Dataflow analysis objective]
TARGET: [System/network to analyze]
FOCUS: [Specific data flows of interest]

REQUIRED OUTPUTS:
- Network topology map
- Communication paths documented
- Protocol analysis
- Data classification by sensitivity
- Trust boundary identification
- Potential interception points

CAPTURE DURATION: [Time window for traffic analysis]
PROTOCOLS: [Specific protocols to focus on]
```

### t7-certificate-agent Prompt Template
```
MISSION: [Certificate/crypto analysis objective]
TARGET: [Endpoints to analyze]
TEST CASE: [TC-010 if applicable]

REQUIRED OUTPUTS:
- Certificate inventory
- Validity period assessment (<=13 months requirement)
- Chain of trust verification
- Cipher suite analysis
- Key strength assessment
- Certificate transparency findings
- Compliance status

STANDARDS: [Specific crypto standards to verify against]
```

### t7-compliance-agent Prompt Template
```
MISSION: [Compliance assessment objective]
TARGET: [System to assess]
FRAMEWORK: [CIS Debian 11 | NIAP PP-OS v4.3 | NIST 800-53 | other]

REQUIRED OUTPUTS:
- Control-by-control assessment
- Pass/Fail status for each control
- Evidence collected
- Remediation recommendations
- Compliance percentage score
- Exception documentation

BENCHMARK VERSION: [Specific version]
PROFILE: [Level 1 | Level 2 | Server | Workstation]
```

### t7-exploitation-agent Prompt Template
```
MISSION: [Exploitation objective]
TARGET: [Vulnerable system/service]
VULNERABILITY: [CVE or vulnerability description]

REQUIRED OUTPUTS:
- Exploit development documentation
- PoC code (if developed)
- Exploitation steps
- Success/failure status
- Post-exploitation position achieved
- Evidence of exploitation

EXPLOIT TYPE: [RCE | privesc | injection | other]
CONSTRAINTS: [Stealth requirements, damage limitations]
EXISTING ACCESS: [Current foothold if any]
```

### t7-cloud-pivot-agent Prompt Template
```
MISSION: [Cloud pivot objective]
TARGET: [Cloud environment - AWS/Azure/GCP]
PIVOT SOURCE: [Current access point]

REQUIRED OUTPUTS:
- Cloud credential discovery
- IAM analysis
- Resource enumeration
- Privilege escalation paths
- Data access achieved
- Persistence options

CLOUD PROVIDER: [AWS | Azure | GCP | multi-cloud]
CURRENT ACCESS: [Compromised credentials/roles]
OBJECTIVE: [Specific cloud resources to access]
```

### t7-reverse-tunnel-agent Prompt Template
```
MISSION: [Tunneling objective]
TARGET: [Network/system to tunnel through]
DESTINATION: [Target endpoint for tunnel]

REQUIRED OUTPUTS:
- Tunnel establishment status
- Covert channel analysis
- Stability assessment
- Detection risk analysis
- Alternative tunnel options
- Persistence of tunnel

TUNNEL TYPE: [SSH | chisel | ligolo | custom]
EGRESS RESTRICTIONS: [Known firewall/proxy rules]
CALLBACK ADDRESS: [Attacker-controlled endpoint]
```

### t7-lateral-movement-agent Prompt Template
```
MISSION: [Lateral movement objective]
SOURCE: [Current compromised system]
DESTINATION: [Target system(s)]

REQUIRED OUTPUTS:
- Movement path documentation
- Credentials used/discovered
- Systems compromised
- Access level on each system
- Detection indicators
- Recommended next movements

TECHNIQUES: [PTH | PTT | PSExec | WMI | SSH | other]
AVAILABLE CREDS: [Credentials available for use]
NETWORK CONTEXT: [Subnet, domain, trust relationships]
```

### t7-data-exfiltration-agent Prompt Template
```
MISSION: [Data exfiltration objective]
TARGET: [Data to exfiltrate]
SOURCE: [Compromised system with data access]

REQUIRED OUTPUTS:
- Data identified and classified
- Exfiltration method used
- Volume of data accessed
- Evidence of capability (not actual exfil in most cases)
- Detection risk assessment
- Data handling compliance

EXFIL METHOD: [HTTP | DNS | ICMP | cloud | physical]
DATA SENSITIVITY: [Classification level]
CONSTRAINTS: [Volume limits, stealth requirements]
```

### t7-persistence-agent Prompt Template
```
MISSION: [Persistence analysis objective]
TARGET: [System to analyze/persist on]
CURRENT ACCESS: [Existing foothold]

REQUIRED OUTPUTS:
- Persistence mechanism inventory
- Recommended persistence techniques
- Detection likelihood for each
- Survivability assessment
- Cleanup procedures
- Evidence collected

PERSISTENCE TYPE: [userland | kernel | firmware | other]
OS TYPE: [Windows | Linux | macOS]
STEALTH REQUIREMENT: [low | medium | high]
```

### t7-social-engineering-agent Prompt Template
```
MISSION: [Social engineering objective]
TARGET: [Target individuals/organization]
PRETEXT: [Cover story/scenario]

REQUIRED OUTPUTS:
- Target reconnaissance
- Pretext development
- Attack vector recommendation
- Success/failure status
- Lessons learned
- Evidence collected

SE TYPE: [phishing | vishing | pretexting | physical]
AUTHORIZATION: [Explicit authorization details]
BOUNDARIES: [What is NOT permitted]
```

### t7-evidence-collection-agent Prompt Template
```
MISSION: [Evidence collection objective]
CONTEXT: [What was found/exploited]
CLASSIFICATION: [Evidence sensitivity level]

REQUIRED OUTPUTS:
- Evidence inventory with hashes
- Chain of custody documentation
- Screenshots/recordings
- Log extracts
- Timestamp correlation
- Storage location

EVIDENCE TYPE: [screenshot | log | artifact | memory | network]
RETENTION: [How long to preserve]
FORMAT: [Required output format]
```

### t7-tools-arsenal-agent Prompt Template
```
MISSION: [Tool management objective]
TASK: [What capability is needed]
ENVIRONMENT: [Target OS/platform]

REQUIRED OUTPUTS:
- Tool recommendation
- Installation steps
- Configuration guidance
- Usage examples
- Alternative tools
- Cleanup procedures

TOOL CATEGORY: [recon | exploit | post-exploit | other]
CONSTRAINTS: [Size limits, detection avoidance]
EXISTING TOOLS: [What's already available]
```

### t7-report-generation-agent Prompt Template
```
MISSION: [Report generation objective]
AUDIENCE: [Executive | Technical | Mixed]
FINDINGS: [Summary of findings to include]

REQUIRED OUTPUTS:
- Executive summary
- Technical findings detail
- Risk ratings
- Remediation roadmap
- Evidence appendix
- Methodology documentation

REPORT TYPE: [Executive | Technical | Full | Custom]
FORMAT: [Markdown | PDF | HTML | Word]
TEMPLATE: [Standard | Formal | Custom]
DEADLINE: [Report due date]
```

### Specialized Agent Templates

### t7-bbhunter Prompt Template
```
MISSION: [Bug bounty objective]
PROGRAM: [Target program - HackerOne/Bugcrowd/etc]
SCOPE: [In-scope targets from program]

REQUIRED OUTPUTS:
- Vulnerability discoveries
- Reproduction steps
- Impact assessment
- Suggested severity
- Draft submission report
- Similar/duplicate check

FOCUS: [Specific vulnerability classes]
TIME BUDGET: [Hours allocated]
PREVIOUS FINDINGS: [Known issues to skip]
```

### t7-malwareanalyst Prompt Template
```
MISSION: [Malware analysis objective]
SAMPLE: [Sample identifier/hash/location]
ANALYSIS TYPE: [Static | Dynamic | Both]

REQUIRED OUTPUTS:
- Sample classification
- Behavioral analysis
- IOC extraction
- C2 identification
- Capabilities assessment
- Detection signatures

SANDBOX: [Available sandbox environment]
TOOLS: [Analysis tools to use]
SAFETY: [Isolation requirements]
```

### t7-pentester Prompt Template
```
MISSION: [Penetration test objective]
TARGET: [Target scope]
TYPE: [External | Internal | Physical]

REQUIRED OUTPUTS:
- Vulnerability findings
- Exploitation results
- Privilege escalation paths
- Lateral movement achieved
- Data access demonstrated
- Remediation priorities

ENGAGEMENT TYPE: [Black | Gray | White box]
RULES OF ENGAGEMENT: [Specific constraints]
TIME WINDOW: [Testing schedule]
```

### t7-pentesterweb Prompt Template
```
MISSION: [Web application test objective]
TARGET: [Application URL/scope]
TYPE: [DAST | SAST | IAST | Manual]

REQUIRED OUTPUTS:
- OWASP Top 10 assessment
- Injection vulnerabilities
- Authentication weaknesses
- Session management issues
- API security findings
- Business logic flaws

AUTHENTICATION: [Credentials if authenticated test]
COVERAGE: [Specific functionality to test]
EXCLUSIONS: [What NOT to test]
```

### t7-redteamer Prompt Template
```
MISSION: [Red team objective]
SCENARIO: [Adversary simulation scenario]
TARGET: [Organization/systems in scope]

REQUIRED OUTPUTS:
- Attack narrative
- TTPs used (MITRE ATT&CK mapping)
- Objectives achieved
- Detection events triggered
- Evasion techniques used
- Lessons learned

ADVERSARY PROFILE: [APT to emulate]
OBJECTIVES: [Flags/goals to achieve]
DURATION: [Engagement timeframe]
```

### t7-threathunter Prompt Template
```
MISSION: [Threat hunting objective]
ENVIRONMENT: [Systems/networks to hunt in]
HYPOTHESIS: [Threat hypothesis to test]

REQUIRED OUTPUTS:
- Hunt findings
- IOC matches
- Anomaly detections
- False positive analysis
- Recommended detections
- Threat assessment

DATA SOURCES: [Logs, EDR, SIEM available]
HUNT TYPE: [IOC-based | TTP-based | Anomaly-based]
TIMEFRAME: [Historical period to analyze]
```

### t7-reviewer Prompt Template
```
MISSION: [Code review objective]
TARGET: [Repository/codebase to review]
FOCUS: [Security | Quality | Both]

REQUIRED OUTPUTS:
- Vulnerability findings in code
- Secure coding violations
- Hardcoded secrets
- Dependency vulnerabilities
- Remediation code suggestions
- Priority ranking

LANGUAGE: [Programming language(s)]
FRAMEWORK: [Frameworks in use]
STANDARDS: [Coding standards to check against]
```

### t7-code-review-agent Prompt Template
```
MISSION: [White-box code analysis objective]
TARGET: [Codebase directory/repository]
SCOPE: [Network-accessible components only | Full codebase]

REQUIRED OUTPUTS:
- Architecture & technology stack analysis
- Authentication & authorization deep dive
- Attack surface analysis (entry points, APIs)
- XSS sinks and render contexts
- SSRF sinks and HTTP client usage
- Injection sinks (SQL, Command, Template)
- Critical file paths categorized by security relevance
- Data security & storage analysis

ANALYSIS DEPTH: [quick | standard | comprehensive]
FOCUS AREAS: [Specific vulnerability classes to prioritize]
OUTPUT FORMAT: deliverables/code_analysis_deliverable.md
```

### t7-xss-specialist Prompt Template
```
MISSION: [XSS analysis/exploitation objective]
TARGET: [Application URL/endpoints]
PHASE: [ANALYSIS | EXPLOITATION]

REQUIRED OUTPUTS (Analysis):
- Source-to-sink traces for all XSS vectors
- Render context classification (HTML_BODY, ATTRIBUTE, JS_STRING, URL, CSS)
- Encoding/sanitization gap analysis
- Vulnerability classification (Reflected, Stored, DOM-based)
- Exploitation queue with witness payloads

REQUIRED OUTPUTS (Exploitation):
- Working payloads for each confirmed vulnerability
- CSP/WAF bypass techniques used
- Impact demonstration (session hijack, data theft)
- Reproducible exploitation steps

INPUT FILES: 
- deliverables/recon_deliverable.md
- deliverables/pre_recon_deliverable.md (XSS sinks section)
- deliverables/xss_exploitation_queue.json (for exploitation)

INTENSITY: [passive | moderate | aggressive]
```

### t7-injection-specialist Prompt Template
```
MISSION: [Injection analysis/exploitation objective]
TARGET: [Application URL/endpoints]
PHASE: [ANALYSIS | EXPLOITATION]
INJECTION_TYPES: [SQLi | CommandInjection | LFI | RFI | SSTI | PathTraversal | Deserialization]

REQUIRED OUTPUTS (Analysis):
- Source-to-sink traces for all injection vectors
- Slot type classification (SQL-val, SQL-ident, CMD-argument, etc.)
- Sanitization coverage analysis
- Post-sanitization concatenation detection
- Exploitation queue with witness payloads

REQUIRED OUTPUTS (Exploitation):
- Database fingerprint and enumeration
- Data extraction proof (first 5 rows from sensitive table)
- Command execution proof (for command injection)
- Reproducible exploitation steps

INPUT FILES:
- deliverables/recon_deliverable.md
- deliverables/pre_recon_deliverable.md (Injection sinks section)
- deliverables/injection_exploitation_queue.json (for exploitation)

DATABASE_TYPE: [MySQL | PostgreSQL | MSSQL | Oracle | SQLite | Unknown]
```

### t7-ssrf-specialist Prompt Template
```
MISSION: [SSRF analysis/exploitation objective]
TARGET: [Application URL/endpoints with URL parameters]
PHASE: [ANALYSIS | EXPLOITATION]

REQUIRED OUTPUTS (Analysis):
- HTTP client usage patterns
- Protocol/scheme validation gaps
- Hostname/IP restriction bypasses
- Port restriction analysis
- URL parsing inconsistencies
- Exploitation queue with suggested techniques

REQUIRED OUTPUTS (Exploitation):
- Internal service access proof
- Cloud metadata retrieval (AWS/Azure/GCP)
- Network reconnaissance results
- Reproducible exploitation steps

INPUT FILES:
- deliverables/recon_deliverable.md
- deliverables/pre_recon_deliverable.md (SSRF sinks section)
- deliverables/ssrf_exploitation_queue.json (for exploitation)

CLOUD_PROVIDER: [AWS | Azure | GCP | Unknown]
INTERNAL_TARGETS: [Known internal IPs/services to test]
```

---
