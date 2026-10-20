## 5. Phase 3: Web Vulnerability Extraction & Hypothesis Generation

Phase 3 transforms the raw ambiguities, grammar seams, and state gaps discovered in Phase 2 into structured, testable, and prioritized web security hypotheses.

### 5.1 The Master Web RFC Analysis Worksheet

Every audited Web RFC must be documented in a standardized Master Web Analysis Worksheet. This artifact preserves the chain of reasoning from specification text to exploit hypothesis:

```
===========================================================================
MASTER WEB RFC ANALYSIS WORKSHEET (Form WEB-RVRM-WS-v4.0)
===========================================================================
A. SPECIFICATION METADATA
---------------------------------------------------------------------------
RFC Number:          RFC ____________
Title:               ______________________________________________________
Authors / Editors:   ______________________________________________________
Publication Date:    ____________    Current Status: [ ] Proposed Standard
                                                     [ ] Internet Standard
                                                     [ ] Best Current Practice
                                                     [ ] Informational
Obsoleted By:        ______________________________________________________
Updates / Updated By:______________________________________________________
Lineage Line:        RFC ____ -> RFC ____ -> RFC ____ [TARGET] -> RFC ____
Zombie References:   References RFC ____ (Obsoleted by RFC ____)
Verified Errata:     ___ Technical (Verified: ___), ___ Editorial
Working Group:       IETF HTTP Working Group (HTTPWG) / Related
Datatracker URL:     https://datatracker.ietf.org/doc/rfc______/

B. NORMATIVE LANGUAGE CENSUS (RFC 2119 / RFC 8174 IN WEB CONTEXT)
---------------------------------------------------------------------------
MUST:                ____        MUST NOT:            ____
SHOULD:              ____        SHOULD NOT:          ____
MAY:                 ____        OPTIONAL:            ____
Total Normative:     ____        Total Page Count:    ____
Ambiguity Ratio (AR):____        Ambiguity Density:   ____ / page
Evaluation:          [ ] High Yield (AR > 0.40)  [ ] Moderate  [ ] Low

C. NORMATIVE AMBIGUITY TARGET LOG (TOP 5 CHOICE POINTS)
---------------------------------------------------------------------------
1. Section ___: "________________________________________________________"
   - Requirement Class: [ ] Defensive  [ ] Degradation  [ ] Optimization
   - Implementation Choice A: _____________________________________________
   - Implementation Choice B: _____________________________________________
   - Divergence Security Consequence: ____________________________________

2. Section ___: "________________________________________________________"
   - Requirement Class: [ ] Defensive  [ ] Degradation  [ ] Optimization
   - Implementation Choice A: _____________________________________________
   - Implementation Choice B: _____________________________________________
   - Divergence Security Consequence: ____________________________________

D. FORMAL ABNF GRAMMAR AUDIT
---------------------------------------------------------------------------
Header / Grammar Rules: ___________________________________________________
Whitespace Rules:       [ ] OWS  [ ] RWS  [ ] BWS  [ ] obs-fold
Delimiter Overlaps:     [ ] Bare LF  [ ] Header Semicolon  [ ] Comma List
Grammar Ambiguities:    ___________________________________________________

E. GENERATIONAL DEPRECATION & DRIFT
---------------------------------------------------------------------------
Predecessor Web Spec:   RFC ____________
Key Features Removed:   ___________________________________________________
Language Weakened:      ___________________________________________________
Language Strengthened:  ___________________________________________________
New Security Warnings:  ___________________________________________________

F. PROTOCOL STATE MACHINE & PROXY BOUNDARIES
---------------------------------------------------------------------------
Documented States:      ___________________________________________________
Undefined Transitions:  Input: ______________ in State: ___________________
Proxy Boundaries:       [ ] H2->H1 Downgrade  [ ] WebSocket Tunnel  [ ] Cache

G. WEB RESEARCH POTENTIAL SCORING (Web-RPSF v2.0)
---------------------------------------------------------------------------
N_impl (1-5): ___ x 3 = ___    L_obso (1-5): ___ x 2 = ___
C_norm (1-5): ___ x 2.5 = ___  E_verif (1-5): ___ x 1.5 = ___
B_proxy (1-5): ___ x 2.5 = ___ U_count (1-5): ___ x 1 = ___
S_gap (0-5):  ___ x 1 = ___
TOTAL WEB-RPSF SCORE:  _____ / 55   [ ] Advance to Phase 3  [ ] Discard
===========================================================================
```

### 5.2 Systematic Hypothesis Derivation Framework for Web Flaws

Hypotheses must not be speculative guesses. They are derived algorithmically through an 8-step deduction process:

```
[ Step 1: Isolate Web Ambiguity ] ──> Locate normative SHOULD/MAY or ABNF friction point
                │
                ▼
[ Step 2: Formulate Choices ]    ──> Identify at least two conflicting proxy/server behaviors
                │
                ▼
[ Step 3: Identify Target Stacks ]──> Map Choice A and Choice B to real web servers (Nginx, Envoy)
                │
                ▼
[ Step 4: Construct Web Pipeline ]──> Model a multi-tier proxy chain pairing Target A & Target B
                │
                ▼
[ Step 5: Define Invariant ]     ──> State the security invariant violated (Framing, ACL, Cache)
                │
                ▼
[ Step 6: Craft Test Vector ]    ──> Synthesize minimal HTTP request triggering the differential
                │
                ▼
[ Step 7: Define Oracle ]        ──> Establish unambiguous HTTP status, header, or body delta
                │
                ▼
[ Step 8: Build Card ]           ──> Populate formal Web Hypothesis Card
```

### 5.3 The Web Hypothesis Card Specification

Each hypothesis is encapsulated in a formal **Web Hypothesis Card**, structured with predicate logic to enable programmatic testing:

```
===========================================================================
WEB-RVRM HYPOTHESIS CARD
===========================================================================
ID:                  H-WEB-{VECTOR}-{NUMBER}
Target Web RFC:      RFC {NUMBER}, Section {X.Y}, Paragraph {Z}
Primary Vector:      [ ] V1: Downgrade      [ ] V2: Deprecated Feature
                     [ ] V3: Ambiguity      [ ] V4: Version Conflict
                     [ ] V5: State Machine  [ ] V6: Extension Abuse
                     [ ] V7: Boundary Desync[ ] V8: Error Handling

FORMAL PREDICATE:
  Given a multi-tier web pipeline composed of Frontend Proxy F and Backend Server B:
  IF crafted HTTP request P is transmitted,
  THEN F evaluates Condition(F, P) == True and executes Action(F),
  WHILE B evaluates Condition(B, P) == False and executes Action(B),
  RESULTING IN web security invariant violation V: 
  {REQUEST_SMUGGLING | CACHE_POISONING | ACL_BYPASS | PRIVILEGE_ESCALATION}

TARGET IMPLEMENTATIONS:
  1. Frontend Proxy F (e.g., Cloudflare, Nginx, Envoy): __________________
  2. Backend Server B (e.g., Node.js, Spring Boot, Gunicorn): ____________

TEST INPUT VECTOR (Minimal Raw HTTP Payload):
  -----------------------------------------------------------------------
  [RAW HTTP REQUEST BYTES / STREAM HERE]
  -----------------------------------------------------------------------

OBSERVATION ORACLE:
  Expected Status Differential:  Frontend: [   ] vs Backend: [   ]
  Header / Body Differential:    ________________________________________
  Timing Delta:                  > ___ ms variance

RISK-ADJUSTED RESEARCH PRIORITY (Web-RARP v2.0):
  Impact (1-5):     ___    Confidence (1-5): ___    Novelty (1-5): ___
  Effort (Hours):   ___    Multiplier:       ___
  CALCULATED WEB-RARP: _____ / 100
===========================================================================
```

### 5.4 Risk-Adjusted Research Priority for Web Flaws (Web-RARP v2.0)

To allocate differential testing time efficiently across dozens of candidate web hypotheses, score each using the **Web-RARP v2.0** formula:

$$\text{Web-RARP} = \left( \frac{\text{Impact} \times \text{Confidence} \times \text{Novelty}}{\sqrt{\text{Verification Effort (Hours)}}} \right) \times M_{web\_exploit}$$

Where parameters are rigorously calibrated for web environments:

1. **Impact ($I \in [1, 5]$)**:
   - 5 = Remote Code Execution (RCE), Unauthenticated Admin Account Takeover.
   - 4 = HTTP Request Smuggling, Global Cache Poisoning, Vertical Privilege Escalation.
   - 3 = Web Application Firewall (WAF) Bypass, Cross-Tenant Data Leakage, Stored XSS.
   - 2 = Reflected Data Tampering, Host Header Injection, Limited SSRF.
   - 1 = Server Fingerprinting, Information Disclosure via Verbose Error Codes.
2. **Confidence ($C \in [1, 5]$)**:
   - 5 = Direct mathematical consequence of Web RFC text; confirmed in server source code.
   - 4 = Clear specification gap; documented in obscure developer discussions/errata.
   - 3 = Plausible inference from architecture; untested on target web stacks.
   - 2 = Speculative differential requiring complex multi-proxy alignment.
   - 1 = Theoretical conjecture without supporting web server evidence.
3. **Novelty ($N \in [1, 5]$)**:
   - 5 = Brand new web attack class; never previously published or presented.
   - 4 = Novel vector applied to an established web protocol family (e.g., H3 desync).
   - 3 = New variation of a known attack pattern (e.g., novel smuggling prefix).
   - 2 = Known vulnerability applied to a modern web framework.
   - 1 = Fully commoditized CVE reproduction.
4. **Verification Effort ($E \in [1, 40]$ hours)**: Estimated wall-clock time required to deploy Docker target containers and automate differential verification.
5. **Web Exploitation Multiplier ($M_{web\_exploit}$)**:
   - $1.5$ = Remotely exploitable without credentials via standard HTTP/1.1 or HTTP/2.
   - $1.2$ = Requires standard user authentication or internal API positioning.
   - $1.0$ = Requires single-packet race condition timing or client interaction.
   - $0.7$ = Requires non-standard reverse proxy configurations or legacy build flags.

---

