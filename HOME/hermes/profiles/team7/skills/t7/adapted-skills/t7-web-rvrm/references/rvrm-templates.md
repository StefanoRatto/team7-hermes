## 10. Master Templates, Worksheets, and Checklists

---

### 10.1 Master Web RFC Analysis Worksheet

*(Copy and populate this worksheet for every Web RFC evaluated in Phase 2)*

```
===========================================================================
WEB-RVRM MASTER RFC ANALYSIS WORKSHEET (Form WEB-RVRM-WS-v4.0)
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
Updates:             ______________________________________________________
Updated By:          ______________________________________________________
Lineage Line:        RFC ____ -> RFC ____ -> RFC ____ [TARGET] -> RFC ____
Zombie References:   References RFC ____ (Which was obsoleted by RFC ____)
Errata Count:        ___ Technical (Verified: ___), ___ Editorial
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

---

### 10.2 Master Web Hypothesis Card

*(Populate for every testable hypothesis derived in Phase 3)*

```
===========================================================================
WEB-RVRM MASTER HYPOTHESIS CARD (Form WEB-RVRM-HC-v4.0)
===========================================================================
Card Identifier:     H-WEB-{VECTOR}-{NUMBER}
Creation Date:       YYYY-MM-DD
Target Web RFC:      RFC {NUMBER}, Section {X.Y}, Page {Z}
Primary Vector:      [ ] V1: Downgrade Persistence
                     [ ] V2: Deprecated Feature Persistence
                     [ ] V3: Specification Ambiguity (Parser Differential)
                     [ ] V4: Version Conflict Analysis
                     [ ] V5: State Machine Discrepancy
                     [ ] V6: Extension Mechanism Abuse
                     [ ] V7: Cross-Protocol Boundary Analysis
                     [ ] V8: Error Handling Divergence

FORMAL SECURITY HYPOTHESIS:
"Given a multi-tier web pipeline composed of [Frontend Proxy] and [Backend Server]:
 IF the crafted HTTP request [Crafted Payload P] is transmitted,
 THEN [Frontend Proxy] will execute [Behavior A],
 WHILE [Backend Server] will execute [Behavior B],
 ENABLING an attacker to [Violate Invariant / Web Impact]."

TARGET IMPLEMENTATIONS:
  1. Frontend Proxy (e.g., Cloudflare, Nginx, Envoy): ___________________
  2. Backend Server (e.g., Node.js, Spring Boot, Gunicorn): _____________

PRECONDITIONS:
  1. ____________________________________________________________________
  2. ____________________________________________________________________

TEST INPUT VECTOR (Minimal Raw HTTP Payload):
  -----------------------------------------------------------------------
  [RAW HTTP REQUEST BYTES / STREAM HERE]
  -----------------------------------------------------------------------

OBSERVATION ORACLE:
  Expected Status Differential:  Target A: [   ] vs Target B: [   ]
  Header / Body Differential:    ________________________________________
  Timing Differential:           > ___ ms variance

RISK-ADJUSTED RESEARCH PRIORITY (Web-RARP v2.0):
  Impact (1-5):     ___    Confidence (1-5): ___    Novelty (1-5): ___
  Effort (Hours):   ___    Multiplier:       ___
  CALCULATED RARP:  _____ / 100
===========================================================================
```

---

### 10.3 Web Differential Testing Execution Matrix

*(Use this operational matrix to record test runs during Phase 4)*

```
+-----------------------------------------------------------------------------------------------------+
| WEB DIFFERENTIAL TESTING RUNNER LOG                                                                 |
| Test Run ID: TR-WEB-_______     Date: ____________     Operator: __________________________________ |
| Hypothesis ID: H-WEB-______     Target Web RFC: RFC ____     Attack Vector: V__                     |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target Stack      | Software &  | Status Code | Response    | Distance to | State Mut. | Finding    |
| Component         | Version     | Returned    | Time (ms)   | Baseline    | Observed?  | Tier       |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target 1 (Nginx)  |             |             |             |             | [ ] Yes    |            |
|                   |             |             |             |             | [ ] No     |            |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target 2 (Apache) |             |             |             |             | [ ] Yes    |            |
|                   |             |             |             |             | [ ] No     |            |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target 3 (Caddy)  |             |             |             |             | [ ] Yes    |            |
|                   |             |             |             |             | [ ] No     |            |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target 4 (Node.js)|             |             |             |             | [ ] Yes    |            |
|                   |             |             |             |             | [ ] No     |            |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
| Target 5 (Envoy)  |             |             |             |             | [ ] Yes    |            |
|                   |             |             |             |             | [ ] No     |            |
+-------------------+-------------+-------------+-------------+-------------+------------+------------+
```

---

### 10.4 End-to-End Web Research Operational Checklist

#### Step 1: Selection & Triage (Time: 30–60 Minutes)
- [ ] Identify candidate Web RFC family (HTTP core, caching, URL, cookies, WebSocket).
- [ ] Download complete Web RFC text, errata list, and active HTTPWG drafts from datatracker.ietf.org.
- [ ] Calculate Web-RPSF v2.0 score; confirm target scores $\ge 25$ points.
- [ ] Verify target is implemented across $\ge 3$ commercial web servers, CDNs, or frameworks.

#### Step 2: Three-Tier Web Specification Audit (Time: 2–4 Hours)
- [ ] Execute automated normative keyword census; calculate Web Ambiguity Ratio ($AR$).
- [ ] Classify all `SHOULD` / `MAY` statements into Defensive, Degradation, Optimization, or Discretionary.
- [ ] Audit ABNF grammars for whitespace rules (`OWS`, `BWS`), bare `LF` tolerance, and delimiter overlaps.
- [ ] Execute `rfcdiff` against predecessor and successor Web RFCs; map all four Semantic Drift categories.
- [ ] Extract explicit and implicit finite state machines; highlight all undefined transition cells ($\delta(s,e)=\bot$).
- [ ] Identify all Zombie References pointing to obsoleted standards (e.g., RFC 2616).

#### Step 3: Hypothesis Derivation & Scoring (Time: 1–2 Hours)
- [ ] Translate each isolated web ambiguity into an algorithmic security hypothesis.
- [ ] Author formal Web Hypothesis Cards with explicit predicates, preconditions, and observation oracles.
- [ ] Calculate Web-RARP v2.0 score for each hypothesis; prioritize top-tier candidates ($\text{RARP} \ge 50$).
- [ ] Map potential vulnerability chains connecting isolated findings into high-impact exploit graphs.

#### Step 4: Testbed & Harness Engineering (Time: 2–6 Hours)
- [ ] Spin up containerized heterogeneous web target matrix (Docker Compose / Kubernetes).
- [ ] Configure targets with baseline configurations and standard HTTP access logging.
- [ ] Configure network proxy / Wireshark capture interface for HTTP packet inspection.
- [ ] Verify baseline HTTP/1.1, HTTP/2, and HTTP/3 connectivity and health check routes.

#### Step 5: Differential Testing & Verification (Time: 2–8 Hours per Hypothesis)
- [ ] Execute differential testing pipeline using automated generator and synchronized transmission.
- [ ] Apply Differential Re-Querying (DRQ) to isolate and mask dynamic response noise (nonces, cookies).
- [ ] If testing concurrency or race conditions, execute the Single-Packet Attack (SPA) with N-1 octet pre-flight.
- [ ] Cluster response outputs using Hierarchical Agglomerative Clustering; confirm deterministic divergence.
- [ ] Validate state persistence: perform follow-up GET requests to prove permanent side-effects.

#### Step 6: Automated Web Tooling Development (Time: 1–2 Weeks)
- [ ] Construct an intentionally vulnerable reference test harness exhibiting each confirmed ambiguity.
- [ ] Build automated detection plugin (Burp Suite Montoya extension or standalone Python async CLI).
- [ ] Implement dual-mode operational controls: Safe Mode (inert canaries) and Aggressive Mode (functional payloads).
- [ ] Verify 100% test coverage against the vulnerable test harness without false positives.

#### Step 7: Responsible Disclosure & Remediation (Time: 1–3 Days)
- [ ] Classify findings: Vendor web server bug vs. IETF specification-level defect.
- [ ] Author standardized Vendor Security Advisories with curl reproduction steps and remediation guidance.
- [ ] For multi-vendor specification defects, open a coordination case with CERT/CC.
- [ ] Submit formal Technical Errata reports to the RFC Editor / IETF HTTP Working Group.
- [ ] If necessary, draft and submit an Internet-Draft (I-D) to the IETF HTTPWG.

---

