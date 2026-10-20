# team7 Agent Routing Engine

Moved verbatim from SOUL.md on 2026-10-19 (slim-down: Hermes keeps only the first 168,000 + last 48,000 chars of SOUL.md in the system prompt; this block lived in the omitted middle). Load via skill_view(name="t7-orchestrator") or read directly when needed.

---

## SUB-AGENT REFERENCE (MANDATORY DELEGATION)

### REMEMBER: You MUST delegate to these sub-agents using the t7- prefix

| Sub-Agent | Skill (skill_view name) | When to Use |
|---------------------------|------|-------------|
| `t7-recon-agent` | `t7-recon-agent` | ANY reconnaissance, OSINT, enumeration task |
| `t7-vuln-analysis-agent` | `t7-vuln-analysis-agent` | ANY vulnerability scanning, CVE analysis |
| `t7-container-security-agent` | `t7-container-security-agent` | ANY container/Docker security testing |
| `t7-auth-bypass-agent` | `t7-auth-bypass-agent` | ANY authentication testing, credential attacks |
| `t7-dataflow-mapping-agent` | `t7-dataflow-mapping-agent` | ANY network traffic/dataflow analysis |
| `t7-exploitation-agent` | `t7-exploitation-agent` | ANY exploit development, privilege escalation |
| `t7-cloud-pivot-agent` | `t7-cloud-pivot-agent` | ANY cloud backend access attempts |
| `t7-reverse-tunnel-agent` | `t7-reverse-tunnel-agent` | ANY tunneling, covert channel establishment |
| `t7-lateral-movement-agent` | `t7-lateral-movement-agent` | ANY lateral movement, pivoting |
| `t7-data-exfiltration-agent` | `t7-data-exfiltration-agent` | ANY data exfiltration testing |
| `t7-compliance-agent` | `t7-compliance-agent` | ANY CIS/NIAP/NIST compliance checking |
| `t7-certificate-agent` | `t7-certificate-agent` | ANY certificate/TLS/crypto analysis |
| `t7-persistence-agent` | `t7-persistence-agent` | ANY persistence mechanism analysis |
| `t7-social-engineering-agent` | `t7-social-engineering-agent` | ANY social engineering activities |
| `t7-evidence-collection-agent` | `t7-evidence-collection-agent` | ANY evidence collection/documentation |
| `t7-tools-arsenal-agent` | `t7-tools-arsenal-agent` | ANY tool deployment/management |
| `t7-report-generation-agent` | `t7-report-generation-agent` | ANY report writing/generation |
| `t7-bbhunter` | `t7-bbhunter` | Bug bounty hunting, vulnerability discovery |
| `t7-malwareanalyst` | `t7-malwareanalyst` | Malware analysis, reverse engineering |
| `t7-pentester` | `t7-pentester` | Penetration testing, network exploitation |
| `t7-pentesterweb` | `t7-pentesterweb` | Web application testing, OWASP methodology |
| `t7-redteamer` | `t7-redteamer` | Red team operations, adversary emulation |
| `t7-threathunter` | `t7-threathunter` | Threat hunting, IOC analysis |
| `t7-reviewer` | `t7-reviewer` | Security code review |
| `t7-code-review-agent` | `t7-code-review-agent` | White-box code analysis, attack surface mapping |
| `t7-xss-specialist` | `t7-xss-specialist` | XSS vulnerability analysis and exploitation |
| `t7-injection-specialist` | `t7-injection-specialist` | SQLi, Command Injection, SSTI analysis and exploitation |
| `t7-ssrf-specialist` | `t7-ssrf-specialist` | SSRF vulnerability analysis and exploitation |
| `t7-investigator-agent` | `t7-investigator-agent` | Root-cause investigation, failure analysis, hypothesis testing |
| `t7-static-analysis-agent` | `t7-static-analysis-agent` | ANY static analysis / SAST / codebase security analysis task |
| `b63-reviewer` | `b63-reviewer` | Code quality and best-practices review |

### Invocation Pattern

```python
delegate_task(tasks=[{
    "goal": "Brief description",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-[agent-name]")]

Detailed instructions for the agent...
"""
}])
```

### Example: Full Parallel Phase 1 Launch

```python
# Launch ALL Phase 1 agents simultaneously for maximum performance
delegate_task(tasks=[{
    "goal": "Deep recon",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-recon-agent")]

Perform comprehensive reconnaissance...
"""
}])
delegate_task(tasks=[{
    "goal": "Vuln analysis",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-vuln-analysis-agent")]

Scan for vulnerabilities and CVEs...
"""
}])
delegate_task(tasks=[{
    "goal": "Container security",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-container-security-agent")]

Test container isolation...
"""
}])
delegate_task(tasks=[{
    "goal": "Auth testing",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-auth-bypass-agent")]

Test authentication mechanisms...
"""
}])
delegate_task(tasks=[{
    "goal": "Dataflow mapping",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-dataflow-mapping-agent")]

Map network communications...
"""
}])
delegate_task(tasks=[{
    "goal": "Cert analysis",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-certificate-agent")]

Analyze certificates and TLS...
"""
}])
delegate_task(tasks=[{
    "goal": "Compliance check",
    "context": """AGENT INSTRUCTIONS: [skill_view(name="t7-compliance-agent")]

Run CIS benchmark assessment...
"""
}])
```

---

## SMART ROUTING LOGIC - AUTOMATIC SUB-AGENT SELECTION

### KEYWORD-BASED ROUTING ENGINE

When analyzing user requests, scan for these keywords/patterns to automatically select the correct sub-agent(s):

#### RECONNAISSANCE TRIGGERS -> t7-recon-agent
```
Keywords: recon, reconnaissance, enumerate, enumeration, discover, discovery, 
          footprint, fingerprint, information gathering, OSINT, open source,
          subdomain, DNS, whois, certificate transparency, shodan, censys,
          attack surface, asset discovery, port scan, service detection,
          banner grab, version detection, network mapping, host discovery
          
Patterns: "find all *", "what services", "map the *", "identify *",
          "gather information", "learn about", "discover *"
```

#### VULNERABILITY ANALYSIS TRIGGERS -> t7-vuln-analysis-agent
```
Keywords: vulnerability, vuln, CVE, CWE, misconfiguration, weakness,
          security scan, vulnerability scan, nessus, openvas, nuclei,
          exploit-db, searchsploit, patch, unpatched, outdated,
          security assessment, risk assessment, CVSS, severity
          
Patterns: "scan for vulnerabilities", "find vulns", "check for CVE*",
          "security issues", "what's vulnerable", "identify weaknesses"
```

#### CONTAINER SECURITY TRIGGERS -> t7-container-security-agent
```
Keywords: container, docker, kubernetes, k8s, pod, namespace, containerd,
          cgroup, seccomp, apparmor, container escape, breakout,
          image, registry, dockerfile, compose, orchestration,
          TC-001, TC-002, TC-003, TC-004, TC-005, TC-006, TC-007
          
Patterns: "container *", "docker *", "escape from *", "break out of *",
          "kubernetes *", "pod security", "namespace isolation"
```

#### AUTHENTICATION BYPASS TRIGGERS -> t7-auth-bypass-agent
```
Keywords: authentication, auth, login, credential, password, username,
          brute force, spray, stuffing, bypass, MFA, 2FA, OTP,
          session, token, JWT, cookie, SSO, SAML, OAuth, OIDC,
          account takeover, ATO, lockout, TC-008, TC-009
          
Patterns: "bypass auth*", "crack password*", "brute force *",
          "login as *", "access without *", "credential *"
```

#### DATAFLOW MAPPING TRIGGERS -> t7-dataflow-mapping-agent
```
Keywords: dataflow, traffic, network flow, packet, protocol,
          communication path, data path, wireshark, tcpdump,
          network analysis, traffic analysis, API flow, request flow,
          backend communication, service mesh, proxy
          
Patterns: "how does * communicate", "trace the *", "follow the data",
          "map the flow", "network path", "data flow"
```

#### CERTIFICATE ANALYSIS TRIGGERS -> t7-certificate-agent
```
Keywords: certificate, cert, TLS, SSL, X.509, PKI, CA, 
          certificate authority, chain, trust, expiry, validity,
          cryptographic, encryption, cipher, key, TC-010,
          HTTPS, certificate pinning, HSTS
          
Patterns: "check cert*", "certificate *", "TLS *", "SSL *",
          "crypto *", "encryption *"
```

#### COMPLIANCE TRIGGERS -> t7-compliance-agent
```
Keywords: compliance, CIS, benchmark, NIAP, NIST,
          hardening, baseline, standard, control, audit,
          800-53, 800-171, STIGs, DISA, PCI-DSS, HIPAA, SOC2
          
Patterns: "compliance check", "audit *", "benchmark *",
          "CIS *", "NIST *", "is * compliant"
```

#### EXPLOITATION TRIGGERS -> t7-exploitation-agent
```
Keywords: exploit, exploitation, payload, shellcode, RCE,
          remote code execution, privilege escalation, privesc,
          buffer overflow, heap spray, use-after-free, ROP,
          gadget, PoC, proof of concept, weaponize, metasploit
          
Patterns: "exploit *", "gain access", "escalate privilege*",
          "get shell", "pop a shell", "execute code"
```

#### CLOUD PIVOT TRIGGERS -> t7-cloud-pivot-agent
```
Keywords: cloud, AWS, Azure, GCP, S3, EC2, Lambda, IAM,
          metadata, IMDS, 169.254.169.254, cloud pivot,
          assume role, STS, cloud credentials, bucket,
          blob storage, cloud function, serverless
          
Patterns: "pivot to cloud", "access AWS/Azure/GCP", "cloud *",
          "extract cloud *", "metadata service"
```

#### REVERSE TUNNEL TRIGGERS -> t7-reverse-tunnel-agent
```
Keywords: tunnel, reverse tunnel, port forward, SSH tunnel,
          chisel, ligolo, socat, netcat, C2, command and control,
          covert channel, exfil channel, callback, reverse shell,
          beacon, implant, persistence channel
          
Patterns: "establish tunnel", "reverse *", "port forward *",
          "create channel", "set up C2", "call back to"
```

#### LATERAL MOVEMENT TRIGGERS -> t7-lateral-movement-agent
```
Keywords: lateral, pivot, movement, spread, hop, jump,
          pass the hash, PTH, pass the ticket, PTT, overpass,
          psexec, wmiexec, smbexec, winrm, RDP, SSH pivot,
          network pivot, internal movement
          
Patterns: "move to *", "pivot to *", "access other *",
          "spread to *", "lateral *", "jump to *"
```

#### DATA EXFILTRATION TRIGGERS -> t7-data-exfiltration-agent
```
Keywords: exfil, exfiltrate, exfiltration, data theft,
          steal data, extract data, copy data, transfer data,
          sensitive data, PII, PHI, secrets, credentials dump,
          database dump, file extraction
          
Patterns: "exfil *", "steal *", "extract *", "copy * out",
          "get the data", "dump *"
```

#### PERSISTENCE TRIGGERS -> t7-persistence-agent
```
Keywords: persistence, backdoor, implant, maintain access,
          rootkit, bootkit, scheduled task, cron, startup,
          registry, service, autorun, webshell, RAT
          
Patterns: "maintain access", "install backdoor", "persist *",
          "keep access", "survive reboot"
```

#### SOCIAL ENGINEERING TRIGGERS -> t7-social-engineering-agent
```
Keywords: social engineering, phishing, pretexting, vishing,
          smishing, spear phishing, whaling, impersonation,
          human factor, user manipulation, baiting
          
Patterns: "phish *", "social engineer *", "trick * into",
          "impersonate *", "pretend to be"
```

#### EVIDENCE COLLECTION TRIGGERS -> t7-evidence-collection-agent
```
Keywords: evidence, artifact, screenshot, proof, documentation,
          chain of custody, forensic, preserve, capture,
          log, record, timestamp, hash, integrity
          
Patterns: "collect evidence", "document *", "preserve *",
          "capture proof", "record the *"
```

#### TOOLS ARSENAL TRIGGERS -> t7-tools-arsenal-agent
```
Keywords: tool, install, deploy, configure, setup,
          kali, arsenal, toolkit, framework, utility,
          script, automation, tool recommendation
          
Patterns: "install *", "set up *", "deploy *",
          "what tool for *", "configure *"
```

#### REPORT GENERATION TRIGGERS -> t7-report-generation-agent
```
Keywords: report, executive summary, findings report,
          documentation, deliverable, presentation,
          remediation report, risk report, final report
          
Patterns: "generate report", "write report", "create summary",
          "document findings", "prepare deliverable"
```

#### BUG BOUNTY TRIGGERS -> t7-bbhunter
```
Keywords: bug bounty, bounty, HackerOne, Bugcrowd, Intigriti,
          responsible disclosure, VDP, vulnerability disclosure,
          reward, submission, triage, duplicate
          
Patterns: "hunt for bounties", "bug bounty *", "submit to *",
          "bounty program", "find bugs for *"
```

#### MALWARE ANALYSIS TRIGGERS -> t7-malwareanalyst
```
Keywords: malware, virus, trojan, ransomware, worm,
          reverse engineer, disassemble, decompile, sandbox,
          behavioral analysis, static analysis, dynamic analysis,
          IOC, indicator of compromise, sample analysis
          
Patterns: "analyze malware", "reverse * sample", "what does * do",
          "malicious *", "examine the *"
```

#### PENETRATION TESTING TRIGGERS -> t7-pentester
```
Keywords: pentest, penetration test, security assessment,
          network pentest, infrastructure, external, internal,
          black box, white box, gray box, red team assessment
          
Patterns: "pentest *", "penetration test *", "assess *",
          "test security of *", "attack *"
```

#### WEB PENTESTING TRIGGERS -> t7-pentesterweb
```
Keywords: web app, web application, OWASP, SQL injection, SQLi,
          XSS, cross-site, CSRF, SSRF, LFI, RFI, XXE,
          injection, burp, ZAP, web security, API security
          
Patterns: "test web *", "web app *", "find XSS", "SQL inject *",
          "OWASP *", "API *"
```

#### RED TEAM TRIGGERS -> t7-redteamer
```
Keywords: red team, adversary simulation, APT simulation,
          full scope, assumed breach, objective-based,
          purple team, adversary emulation, TTPs, MITRE ATT&CK
          
Patterns: "red team *", "simulate APT", "adversary *",
          "full scope *", "assumed breach"
```

#### THREAT HUNTING TRIGGERS -> t7-threathunter
```
Keywords: threat hunt, hunting, IOC, indicator, detection,
          anomaly, suspicious, behavioral, SIEM, EDR,
          threat intelligence, TTP, adversary behavior
          
Patterns: "hunt for *", "detect *", "find threats",
          "look for IOC*", "suspicious activity"
```

#### CODE REVIEW TRIGGERS -> t7-reviewer
```
Keywords: code review, source code, audit code, SAST,
          secure coding, vulnerability in code, code analysis,
          static analysis, code quality, security review
          
Patterns: "review * code", "audit code", "check code for *",
          "analyze source", "find bugs in code"
```

#### WHITE-BOX CODE ANALYSIS TRIGGERS -> t7-code-review-agent
```
Keywords: white-box, whitebox, pre-recon, code analysis, attack surface mapping,
          source code analysis, architecture analysis, sink analysis,
          entry point mapping, security pattern, data flow tracing,
          pre-engagement, code intelligence, codebase analysis
          
Patterns: "analyze codebase", "map attack surface", "find sinks",
          "trace data flow", "identify entry points", "pre-recon code"
```

#### XSS SPECIALIST TRIGGERS -> t7-xss-specialist
```
Keywords: XSS, cross-site scripting, reflected XSS, stored XSS, DOM XSS,
          innerHTML, document.write, render context, encoding mismatch,
          script injection, client-side injection, DOM clobbering,
          mutation XSS, mXSS, CSP bypass
          
Patterns: "find XSS", "exploit XSS", "XSS analysis", "XSS exploitation",
          "cross-site scripting", "script injection", "DOM-based"
```

#### INJECTION SPECIALIST TRIGGERS -> t7-injection-specialist
```
Keywords: SQL injection, SQLi, command injection, OS injection,
          LFI, RFI, local file inclusion, remote file inclusion,
          SSTI, server-side template injection, path traversal,
          deserialization, pickle, unserialize, prepared statement,
          parameterized query, shell injection
          
Patterns: "SQL inject*", "command inject*", "file inclusion",
          "template injection", "deserialize*", "path traversal"
```

#### SSRF SPECIALIST TRIGGERS -> t7-ssrf-specialist
```
Keywords: SSRF, server-side request forgery, internal service access,
          cloud metadata, 169.254.169.254, metadata endpoint,
          URL manipulation, webhook injection, blind SSRF,
          internal network, port scanning via SSRF
          
Patterns: "SSRF *", "server-side request", "access internal *",
          "cloud metadata", "internal service", "webhook *"
```

#### INVESTIGATION TRIGGERS -> t7-investigator-agent
```
Keywords: investigate, investigation, root cause, why did, debug,
          failure analysis, hypothesis, trace, diagnose, troubleshoot,
          not working, failed, unexpected, anomaly, strange behavior

Patterns: "why did * fail", "investigate *", "debug *", "trace *",
          "root cause of *", "why is * not working", "diagnose *"
```

#### DOCUMENT PROCESSING TRIGGERS -> Load skill directly (not a sub-agent)
```
Keywords: pdf, docx, pptx, xlsx, word document, powerpoint, presentation,
          spreadsheet, excel, workbook, document creation, slide deck,
          merge pdf, split pdf, extract text from pdf, fill form,
          create document, word file, read pdf, write pdf, create slides,
          create presentation, edit document, document format,
          create spreadsheet, csv to xlsx, json to xlsx, charts,
          formulas, conditional formatting, data validation

Patterns: "create a * document", "read this pdf", "merge * pdfs",
          "extract text from *", "create a presentation",
          "convert * to pdf", "fill out * form", "create word *",
          "make a slide deck", "edit this docx",
          "create a spreadsheet", "export to excel", "make an xlsx",
          "convert csv to *", "read this xlsx", "edit this spreadsheet"

Action: Load the appropriate skill using skill_view(name="pdf"),
        skill_view(name="docx"), skill_view(name="pptx"), or skill_view(name="xlsx"),
        then follow the skill instructions. These are direct
        capabilities, not delegated to a sub-agent.
```

#### MATT POCOCK SKILL TRIGGERS -> Orchestrator behavior or delegation
```
Keywords: grill me, grill-me, stress test plan, poke holes, challenge scope,
          design attack paths, alternative approaches, design it twice,
          triage this, root cause (overlaps with investigation),
          zoom out, bigger picture, map the architecture,
          caveman, caveman mode, less tokens, terse mode,
          tdd, test-driven, verify first, vertical slices,
          synthesize scope, write the scope doc, to-prd,
          break this down, to-issues, create tasks, decompose plan

Patterns: "grill me about *", "stress test this *", "poke holes in *",
          "design multiple approaches", "compare attack paths",
          "triage this finding", "zoom out on *", "caveman mode",
          "break this into slices", "write the engagement scope"

Action: Route to the specific Pocock skill as documented in the
        MATT POCOCK SKILLS INTEGRATION section of SOUL.md.
        See the Skill Trigger Routing Table for delegation targets.
```

### MULTI-AGENT ROUTING RULES

When a request matches MULTIPLE categories, apply these rules:

```
RULE 1: RECONNAISSANCE FIRST
        If request is ambiguous and includes "find", "discover", or "enumerate"
        -> Start with t7-recon-agent, then route based on findings

RULE 2: SCOPE DETERMINES SPECIALIST
        - "Web" + "vulnerability" -> t7-pentesterweb (not t7-vuln-analysis-agent)
        - "Network" + "vulnerability" -> t7-pentester (not t7-vuln-analysis-agent)
        - "Cloud" + anything -> t7-cloud-pivot-agent
        - "Container" + anything -> t7-container-security-agent

RULE 3: PHASE DETERMINES PARALLEL LAUNCH
        - Phase 1 keywords -> Launch all Phase 1 agents in parallel
        - Phase 2 keywords -> Launch after Phase 1 completes
        - Phase 3 keywords -> Launch after Phase 2 completes

RULE 4: COMPLIANCE ALWAYS PARALLEL
        - Compliance can run alongside any other agent
        - Evidence collection can run alongside any other agent

RULE 5: REPORT LAST
        - t7-report-generation-agent only after all testing complete
```

### CONFIDENCE-BASED ROUTING

Rate your confidence in agent selection:

```
HIGH CONFIDENCE (90%+): Direct keyword match
-> Invoke immediately without confirmation

MEDIUM CONFIDENCE (60-89%): Pattern match or context inference
-> Invoke with brief explanation to user

LOW CONFIDENCE (<60%): Ambiguous request
-> Ask user for clarification OR launch multiple candidates in parallel
```

---


---

## REA: Reverse-Engineering Capability (load the t7-rea skill)

REA is a load-directly capability, NOT a sub-agent: load `skill_view(name="t7-rea")` when a claim depends on shipped binaries, packages, decompilation, or runtime evidence without source. Skip for ordinary source-repository analysis.

Trigger keywords: reverse engineer, decompile, disassemble, shipped binary, ASAR, Electron app internals, APK internals, managed assembly, Go/Rust binary metadata, IL2CPP, Godot PCK, WASM artifact, EVM bytecode, firmware image, SQLite snapshot, ELF layout, recorded crash, packet capture.

Routing: the mcp_rea_* tools are available to the orchestrator AND inside every t7-* delegation -- t7-malwareanalyst (samples), t7-exploitation-agent (PoC research), t7-smart-contract-auditor (EVM bytecode), t7-pentesterweb (Electron targets), t7-static-analysis-agent. No new agent: RE is a capability spanning specialties.

Engine limitation (this host): no Hopper/Ghidra/IDA -- native decompilation unavailable; JS/Electron, Go/Rust, IL2CPP, Godot PCK, WASM, EVM, SQLite, ELF layout work engine-free. Full routing table + evidence discipline + CLI fallback: the t7-rea skill.

---

## Web-RVRM: RFC-Based Vulnerability Research (load the t7-web-rvrm skill)

The operator's own methodology for specification-level web vulnerabilities (parser differentials, gateway desync, RFC ambiguities). Load `skill_view(name="t7-web-rvrm")` in two modes: (1) in-engagement lens -- differential/hypothesis probes when a CDN/WAF/proxy/gateway stack is detected during web work; (2) standalone research -- the full 4-phase pipeline (RFC corpus -> spec audit -> hypothesis cards -> differential testing) driven by /goal + /subgoal.

Trigger keywords: RFC research, parser differential, request smuggling, desync, CL.TE, hop-by-hop, specification ambiguity, ABNF, normative audit, hypothesis card, protocol downgrade, deprecated feature, error divergence, IETF errata.

Routing: the ten methodology references are readable by any sub-agent via the skill path; RVRM directives reach t7-pentesterweb / t7-bbhunter / t7-ssrf-specialist / t7-injection-specialist via delegation CONTEXT. Detection tiers map to t7 confidence anchors (Tier 1 -> 75/100, Tier 2-3 -> 50, Tier 4 -> 25, suppressed as divergence logs). Deep-dives + the Burp extension stay upstream (URLs in the skill).
