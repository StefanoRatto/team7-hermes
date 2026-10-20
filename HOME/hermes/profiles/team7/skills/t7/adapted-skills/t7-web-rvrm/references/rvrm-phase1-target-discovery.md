## 3. Phase 1: High-Yield Web RFC Target Discovery & Corpus Construction

Vulnerability research yield in web protocols is largely determined before reading a single line of specification text. Phase 1 provides an algorithmic methodology for selecting Web RFCs with maximum latent ambiguity, generational conflict, and widespread real-world adoption.

### 3.1 The Web RFC Standards Track & Maturity Processes

The primary body responsible for standardizing the core web transport layer is the **IETF HTTP Working Group (HTTPWG)**, operating alongside the W3C and the WHATWG. Documents progress through the IETF standards process defined in RFC 2026 and simplified in RFC 6410:

```
+-------------------------------------------------------------------------+
| WEB RFC STANDARDS TRACK LIFECYCLE                                       |
|                                                                         |
|  [ Proposed Standard ] ───(Multi-Vendor Web Interoperability)───>       |
|          │                                                              |
|          ▼                                                              |
|  [ Internet Standard (STD) ] (Only HTTP/1.1 STD 97 / 99 reach this!)    |
+-------------------------------------------------------------------------+
| Non-Standards Track Categories Relevant to Web Security                 |
|                                                                         |
|  [ Best Current Practice (BCP) ]  (e.g., BCP 56 on Web Architecture)    |
|  [ Informational ]                (e.g., HTTP/1.0 RFC 1945)             |
|  [ Historic ]                     (e.g., Deprecated Digest Auth)        |
+-------------------------------------------------------------------------+
```

#### The "Proposed Standard" Trap in Web Technologies
Under RFC 6410, an RFC reaches **Proposed Standard** status with **no requirement for multi-vendor interoperability testing**. It requires only rough consensus in the working group. Consequently:
- More than **95% of active Web RFCs remain at "Proposed Standard" permanently**.
- Critical web extensions (e.g., RFC 5789 PATCH, RFC 6455 WebSocket) never advanced to full Internet Standard, preserving unaddressed ambiguities for over a decade.
- **Web-RVRM Golden Rule**: High-impact research focuses aggressively on long-lived Proposed Standards that have ubiquitous deployment across modern web servers and frameworks.

### 3.2 Web RFC Lineage Mapping & Transitive Obsolescence Graph Construction

Web protocol specifications evolve across multi-document obsolescence chains. To discover architectural seams, researchers must construct the **Transitive Web Obsolescence Directed Acyclic Graph (DAG)**:

```
HTTP Core Specification Lineage Graph

RFC 1945 (HTTP/1.0, Informational, 1996)
   │
   ▼
RFC 2068 (HTTP/1.1, Proposed Standard, 1997) [Obsoleted by RFC 2616]
   │
   ▼
RFC 2616 (HTTP/1.1, Draft Standard, 1999) [The "Monolith" - Obsoleted by RFC 7230-7235]
   │
   ├──────────────────────────────────────────────────────┐
   ▼                                                      ▼
RFC 7230-7235 (HTTP/1.1 Suite, 2014)          RFC 5789 (PATCH Method, 2010)
[Split into 6 docs, Obsoleted by 9110-9112]   [STANDALONE EXTENSION!]
   │                                           - References obsoleted RFC 2616!
   ▼                                           - Never updated for RFC 7230!
RFC 9110-9112 (HTTP Semantics & Framing, 2022)- Never updated for RFC 9110!
[Current Core Standard]                        [ZOMBIE REFERENCE ATTACK SURFACE]
```

#### The "Zombie Reference" Phenomenon in Web Protocols

A **Zombie Reference** occurs when an active, widely implemented web extension normatively references a predecessor specification that has been officially obsoleted.
- **Why it matters**: Developers implementing the extension (e.g., a PATCH parser or WebSocket handler) read the referenced document (RFC 2616), inheriting parsing rules that were explicitly deprecated, fixed, or banned in successor specifications (RFC 7230 or RFC 9112).
- When a modern web server combines an RFC 9112 frontend with a plugin or backend framework built against an RFC 2616 zombie reference, the two tiers interpret the same stream through mutually contradictory rules.

### 3.3 The Web Research Potential Scoring Formula (Web-RPSF v2.0)

To prioritize candidate Web RFCs objectively, evaluate each document using the calibrated **Web Research Potential Scoring Formula (Web-RPSF v2.0)**:

$$\text{Web-RPSF} = (3 \times N_{impl}) + (2 \times L_{obso}) + (2.5 \times C_{norm}) + (1.5 \times E_{verif}) + (2.5 \times B_{proxy}) + (1 \times U_{count}) + S_{gap}$$

Where parameters are scored on defined integer scales (1 to 5):

| Metric | Variable | Scale (1-5) | Operational Meaning in Web Research |
|---|---|---|---|
| **Web Implementation Count** | $N_{impl}$ | 1 = Niche web library; 3 = Common web framework; 5 = Ubiquitous (Nginx, Apache, Envoy, Cloudflare, Browsers) | High deployment count guarantees divergent parsing decisions. |
| **Obsolescence Depth** | $L_{obso}$ | 1 = New spec; 3 = 1-2 predecessor versions; 5 = $\ge 3$ generational rewrites (e.g., HTTP/1.1) | Longer lineages accumulate legacy backward-compatibility quirks. |
| **Normative Ambiguity Density** | $C_{norm}$ | $\frac{\text{SHOULD} + \text{MAY} + \text{OPTIONAL}}{\text{Page Count}}$. Scaled 1 ($<0.5$/pg) to 5 ($>3.0$/pg) | Density of implementation choice points where behaviors will diverge. |
| **Verified Errata Count** | $E_{verif}$ | 1 = 0 errata; 3 = 1-3 errata; 5 = $\ge 4$ technical errata | Official confirmation that web implementers are confused by the text. |
| **Proxy Boundary Touchpoints** | $B_{proxy}$ | 1 = End-to-end only; 3 = Touches reverse proxies; 5 = Multi-proxy translation boundary (H2/H1, H3/H1, WebSocket) | Translation and encapsulation boundaries create assumption violations. |
| **Update Frequency** | $U_{count}$ | 1 = Never updated; 3 = 1-2 small updates; 5 = $\ge 3$ active update documents | Patchwork specifications create fragmented compliance. |
| **Security Gap Bonus** | $S_{gap}$ | 0 = Exhaustive security section; 5 = Security section missing or $< 1$ page | Unaddressed threat model in the original design. |

#### Prioritization Thresholds for Web RFCs
- **Web-RPSF $\ge 40$**: **Tier W0 (Immediate High-Yield)**. Exceptional research potential. High probability of multi-vendor request smuggling, gateway desync, or authorization bypass.
- **Web-RPSF 25–39**: **Tier W1 (Strong Potential)**. Prime targets for differential testing and web framework bypasses.
- **Web-RPSF 15–24**: **Tier W2 (Moderate)**. Requires a pre-formulated attack hypothesis.
- **Web-RPSF $< 15$**: **Discard**. Highly specified or client-only utility specifications.

### 3.4 Exhaustive Catalog of Web RFC Families (Priority Tiers W0 to W3)

The following reference matrix categorizes the core protocol families of the web stack, ranked by historical vulnerability yield:

#### Priority Tier W0: Foundational Web Transport & Addressing (Continuous Yield)

| Protocol Family | Core Web RFC Lineage | Critical Ambiguity & Attack Vectors |
|---|---|---|
| **HTTP Semantics & Framing (H1, H2, H3)** | RFC 1945 $\to$ 2068 $\to$ 2616 $\to$ 7230-7235 $\to$ 9110-9114 | Request smuggling (CL.TE, TE.CL, TE.TE), header folding (`obs-fold`), chunk extension abuse, binary pseudo-header injection, HPACK/QPACK compression bombs. |
| **Web Caching Architecture** | RFC 7234 $\to$ RFC 9111 | Keyed vs. unkeyed header differentials, cache deception, cache invalidation failure on unsafe methods (PATCH, POST), delimiter-based cache poisoning. |
| **URI / URL Syntax & Resolution** | RFC 2396 $\to$ RFC 3986, RFC 3987 (IRI) vs. WHATWG URL Standard | Path normalization divergence, backslash vs. forward slash conversion, userinfo `@` authority confusion, fragment `#` vs. query `?` precedence, IPv4 octal/hex/dword SSRF bypasses. |

#### Priority Tier W1: State, Mutation & Tunneling (High Yield)

| Protocol Family | Core Web RFC Lineage | Critical Ambiguity & Attack Vectors |
|---|---|---|
| **HTTP State Management (Cookies)** | RFC 2109 $\to$ 2965 $\to$ 6265 $\to$ 6265bis | Delimiter parsing (semicolon vs. comma), `SameSite` attribute enforcement differentials between proxies and browsers, domain matching prefix confusion, `__Host-` / `__Secure-` prefix bypass. |
| **WebSockets & Stream Upgrades** | RFC 6455, RFC 8441 (WebSocket over H2) | Hop-by-hop `Upgrade` header stripping, reverse-proxy TCP tunnel persistence, masking key differentials, boundary injection of pipelined HTTP requests. |
| **REST Resource Mutation & Patching** | RFC 5789 (PATCH), RFC 6902 (JSON Patch), RFC 7396 (Merge Patch), RFC 6901 | Content-Type confusion, non-atomic mutation race conditions (SPA), missing resource creation ACL bypass, JSON Pointer path traversal (`/../../`). |
| **Web Origin & Boundary Security** | RFC 6454 (Origin), W3C CORS, RFC 6797 (HSTS) | Underspecified behavior for `null` origin reflection, wildcard CORS misconfigurations, pre-flight caching confusion, HSTS header stripping. |

#### Priority Tier W2: Metadata, Forwarding & Authentication (Targeted Yield)

| Protocol Family | Core Web RFC Lineage | Critical Ambiguity & Attack Vectors |
|---|---|---|
| **HTTP Structured Fields & Digest** | RFC 8941 (Structured Fields), RFC 3230, RFC 5843 | Type confusion in structured dictionary/list parsing, integer overflow in header values, digest validation bypasses. |
| **Proxy Forwarding Protocols** | RFC 7239 (`Forwarded`), HAProxy PROXY Protocol v1/v2 | Client IP spoofing via multi-proxy header chaining, unauthenticated PROXY header injection, header truncation via null-bytes. |
| **HTTP Authentication Schemes** | RFC 7235, RFC 7617 (Basic), RFC 7616 (Digest), RFC 6749/6750 (OAuth Bearer) | Token confusion, realm injection, scope downgrade, header parameter injection in `WWW-Authenticate` and `Authorization`. |

#### Priority Tier W3: Auxiliary Web Mechanisms (Contextual Yield)

| Protocol Family | Core Web RFC Lineage | Critical Ambiguity & Attack Vectors |
|---|---|---|
| **Web Linking & Pre-fetching** | RFC 8288 (Web Linking) | Server-Side Request Forgery via `Link: <url>; rel=preload`, cache poisoning via unkeyed link headers. |
| **Range Requests & Partial Content** | RFC 7233 $\to$ RFC 9110 §14 | Overlapping range denial-of-service ("Apache Killer" variants), multipart boundary confusion. |

---

