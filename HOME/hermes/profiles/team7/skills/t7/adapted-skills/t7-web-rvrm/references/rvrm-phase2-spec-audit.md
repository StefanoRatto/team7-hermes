## 4. Phase 2: Evaluating Web Specification Weaknesses (The Three-Tier Audit)

Once a candidate Web RFC is selected, it undergoes the **Three-Tier Web Specification Audit**. This structured analytical procedure dissects the natural language, formal ABNF grammar, generational diffs, and implicit finite state automata of the target document.

```
+-------------------------------------------------------------------------+
| THREE-TIER WEB SPECIFICATION AUDIT ARCHITECTURE                         |
|                                                                         |
|  [ TIER 1: Normative Language Audit ]                                   |
|    - RFC 2119 / RFC 8174 keyword extraction in web contexts             |
|    - Web Ambiguity Ratio (AR) & Ambiguity Density (AD) calculation      |
|    - 4 Functional Classes of SHOULD in web specifications               |
|                                                                         |
|  [ TIER 2: Formal Grammar (ABNF / RFC 5234) Decomposition of HTTP ]     |
|    - Whitespace spectrum analysis (OWS, RWS, BWS, obs-fold)             |
|    - Delimiter ambiguity & terminal token boundary parsing              |
|    - Bare LF vs CRLF line-termination tolerance                         |
|                                                                         |
|  [ TIER 3: Deprecation Diff & Semantic Drift Across HTTP Generations ]   |
|    - rfcdiff across RFC 2616 -> RFC 7230 -> RFC 9112                    |
|    - Semantic narrowing, broadening, inversion, and deletion tracking   |
|    - Zombie reference and legacy backward-compatibility mapping         |
+-------------------------------------------------------------------------+
```

### 4.1 Tier 1: Normative Language Audit (RFC 2119 & RFC 8174 in Web Standards)

Web specifications define requirement levels using the conventions established in **RFC 2119** and updated by **RFC 8174**.

#### The Capitalization Rule (RFC 8174) in Web Implementations
RFC 8174 clarifies that normative keywords have their special meaning **ONLY when they appear in all uppercase**. When they appear in lowercase or title case, they represent standard English prose.
- **Vulnerability Vector**: Web server engines developed before 2017 often treated lowercase keywords (e.g., "should", "may") as normative mandates. Conversely, modern developers frequently assume a lowercase "must" in an RFC is an absolute requirement when it lacks normative weight under RFC 8174.

#### The Four Functional Classes of `SHOULD` in Web RFCs

Every `SHOULD` in a Web RFC represents an explicit decision by the working group to **permit implementation divergence**. An auditor must classify every `SHOULD` into one of four functional categories:

| Functional Class | Web Specification Template | Vulnerability Implication When Ignored |
|---|---|---|
| **Class A: Defensive Verification** | *"Proxies SHOULD verify that Content-Length matches body octets before forwarding."* | **HTTP Request Smuggling**. Proxies that skip verification forward mismatched messages to backends that desynchronize. |
| **Class B: Graceful Degradation** | *"A server SHOULD ignore unrecognized header field parameters."* | **Security Filter Bypass**. Permissive handling of unknown parameters allows attackers to smuggle uninspected control tokens past WAFs. |
| **Class C: Performance Optimization** | *"Caches SHOULD invalidate Request-URI entries upon receiving unsafe methods."* | **Web Cache Poisoning**. Proxies that omit invalidation continue serving stale, attacker-modified cached representations. |
| **Class D: Discretionary Formatting** | *"A sender SHOULD NOT generate obsolete line folding (obs-fold)."* | **Parser Differentials**. Senders generating line folds trigger divergent interpretation across upstream and downstream proxies. |

#### Web Ambiguity Metrics

Quantify the specification's vagueness using two mathematical indices:

1. **Web Ambiguity Ratio ($AR$)**:
   $$AR = \frac{\text{SHOULD} + \text{SHOULD NOT} + \text{RECOMMENDED} + \text{MAY} + \text{OPTIONAL}}{\text{MUST} + \text{MUST NOT} + \text{SHALL} + \text{SHALL NOT} + \text{SHOULD} + \text{SHOULD NOT} + \text{RECOMMENDED} + \text{MAY} + \text{OPTIONAL}}$$
   The numerator includes all discretionary keywords (SHOULD, SHOULD NOT, RECOMMENDED, MAY, OPTIONAL). The denominator includes all RFC 2119 / RFC 8174 normative keywords. SHALL and SHALL NOT are counted as strict requirements alongside MUST and MUST NOT.
   - **$AR > 0.40$**: Extremely high research yield. The specification provides implementers immense freedom to diverge.

2. **Web Ambiguity Density ($AD$)**:
   $$AD = \frac{\text{SHOULD} + \text{SHOULD NOT} + \text{RECOMMENDED} + \text{MAY} + \text{OPTIONAL}}{\text{Total Page Count}}$$
   - **$AD > 2.0$ keywords/page**: Indicates an underspecified or politically contentious web standard where consensus was achieved by diluting requirements into discretionary recommendations.

### 4.2 Tier 2: Formal Grammar (ABNF / RFC 5234) & Syntactic Decomposition of HTTP

Most Web RFCs define protocol syntax using **Augmented Backus-Naur Form (ABNF)**, standardized in **RFC 5234**. Auditing the ABNF exposes fundamental syntactic ambiguities.

#### The HTTP Whitespace Spectrum Vulnerability
Modern HTTP specifications (RFC 7230 §3.2.3, RFC 9112 §5) define four distinct classes of whitespace:
- `OWS` = `*( SP / HTAB )` (Optional Whitespace: zero or more spaces or horizontal tabs)
- `RWS` = `1*( SP / HTAB )` (Required Whitespace: at least one space or tab)
- `BWS` = `OWS` (Bad Whitespace: historically permitted, now forbidden; MUST NOT be generated, MUST be parsed or rejected)
- `obs-fold` = `CRLF 1*( SP / HTAB )` (Obsolete Line Folding: multi-line header fields)

```
ABNF Parsing Differential Scenario Across HTTP Generations:

RFC 2616: Permits obs-fold anywhere within header field values.
RFC 7230: Deprecates obs-fold; proxies SHOULD replace with SP, backends MAY reject.
RFC 9112: Formally mandates: "A server MUST reject with 400 (Bad Request)..."

Resulting Differential Matrix:
Input: "Header: Value1\\r\\n Value2"
- Legacy Server (RFC 2616): Interprets as "Header: Value1 Value2"
- Modern Proxy (RFC 7230): Strips CRLF, forwards "Header: Value1 Value2"
- Strict Backend (RFC 9112): Rejects with 400 Bad Request OR desynchronizes connection!
```

#### Terminal Token and Delimiter Ambiguities
Look for rules where terminal delimiters overlap with permitted character sets:
1. **Bare `LF` vs `CRLF`**: RFC 5234 defines `CRLF = %d13.10`. However, legacy web servers written in C often use `strchr(buf, '\n')`, accepting bare line feeds (`0x0A`). A frontend proxy enforcing `CRLF` does not see a bare `LF` as a line terminator, whereas a backend will split the message at the bare `LF`, creating **HTTP Request Smuggling**.
2. **Header Semicolons and Parameter Separators**: Headers such as `Content-Type`, `Cookie`, and `Transfer-Encoding` use semicolons (`;`) or commas (`,`) to delimit parameters. Mismatched precedence creates type coercion and parameter confusion.
3. **Leading/Trailing Whitespace in Header Names**: RFC 9112 §5.1 forbids whitespace between header name and colon (`Header : Value`). Older servers strip the space and accept; modern reverse proxies treat it as a syntax error or forward it unmodified.

### 4.3 Tier 3: Deprecation Diff & Semantic Drift Analysis Across HTTP Generations

Web RFCs have undergone three major generational overhauls (RFC 2616 $\to$ RFC 7230-7235 $\to$ RFC 9110-9112). Using the IETF `rfcdiff` tool and structural semantic comparison, map all changes into four **Semantic Drift Categories**:

```
+-------------------------------------------------------------------------+
| THE FOUR CATEGORIES OF SEMANTIC DRIFT IN WEB STANDARDS                  |
|                                                                         |
| 1. SEMANTIC NARROWING                                                   |
|    RFC 2616: Permitted obs-fold, space before colon, bare LF.           |
|    RFC 9112: MUST reject obs-fold with 400, MUST reject space before :. |
|    Vector: Legacy backends accept characters that modern WAFs pass!     |
|                                                                         |
| 2. SEMANTIC BROADENING                                                  |
|    RFC 7230: Restricted URI characters strictly to ASCII.              |
|    RFC 9110: Permits extended character encodings in query components. |
|    Vector: Legacy middleboxes truncate or crash on modern syntax!       |
|                                                                         |
| 3. REQUIREMENT INVERSION                                                |
|    RFC 2616 §14.41: Transfer-Encoding: identity was formally valid.     |
|    RFC 7230 §3.3.1: Transfer-Encoding: identity explicitly deleted.    |
|    Vector: Implementations follow opposing requirements simultaneously! |
|                                                                         |
| 4. UNRECONCILED DELETION                                                |
|    RFC 2616 §14.15: Content-MD5 fully specified for integrity checks.  |
|    RFC 7231: Content-MD5 deleted without standard replacement.          |
|    Vector: Legacy code persists forever; specification is silent!       |
+-------------------------------------------------------------------------+
```

### 4.4 Web Protocol State Machine Extraction & Undefined Transitions

Every web protocol specification defines a connection and request state machine. To discover state discrepancy vulnerabilities:

1. **Construct the Formal State Transition Matrix**: Map every documented HTTP state (e.g., `IDLE`, `RECEIVING_HEADERS`, `EXPECT_100`, `PROCESSING_BODY`, `TUNNELING`) against every possible network input event.
2. **Identify the Unspecified Cells ($\delta(s, e) = \bot$)**:
   - What happens if a `PATCH` request arrives while the connection is awaiting a `100-continue` response?
   - What happens if a `DATA` frame arrives on an HTTP/2 stream in the `HALF_CLOSED (remote)` state?
   - What happens if pipelined requests arrive over a connection that is transitioning to a WebSocket tunnel?
3. **Analyze Out-of-Order Execution**:
   - Can an attacker send step 3 before step 1?
   - If an HTTP/2 client transmits `RST_STREAM` before the server has finished allocating the request context, does the server leak worker threads?

### 4.5 Phase 2 Web Go/No-Go Decision Matrix

Before proceeding to hypothesis generation and differential testing, score the candidate Web RFC against the Phase 2 Decision Matrix. A candidate must achieve a score of **$\ge 20$ points** to proceed:

| Evaluation Factor | Scoring Criteria | Points (0-5) |
|---|---|---|
| **Normative Ambiguity Score** | $AR > 0.40$ (5 pts); $AR > 0.25$ (3 pts); $AR < 0.25$ (0 pts) | |
| **ABNF Delimiter Friction** | Overlapping delimiters, bare LF tolerance, or whitespace ambiguity present (5 pts) | |
| **Generational Lineage Drift** | $\ge 1$ Requirement Inversion or Unreconciled Deletion detected (5 pts) | |
| **State Machine Undefined Cells** | $\ge 3$ reachable undefined state transitions identified (5 pts) | |
| **Zombie Reference Factor** | Target references an obsoleted specification (e.g., RFC 2616) for core behavior (5 pts) | |
| **Heterogeneous Web Deployment** | Implemented across $\ge 3$ distinct web server/proxy engines (Nginx, Apache, Envoy, etc.) (5 pts) | |
| **Total Audit Score** | **Threshold: $\ge 20$ points required to advance to Phase 3** | **/30** |

---

