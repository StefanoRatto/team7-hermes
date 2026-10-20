## 7. The Eight Web Research Vectors: Exhaustive Technical Deep-Dives

Every specification-level web vulnerability maps to one of eight core research vectors. These vectors serve as specialized analytical lenses when evaluating any Web RFC.

---

### 7.1 Vector 1: Web Protocol Downgrade Persistence

#### Theoretical Premise & Architecture
Web protocol evolution introduces stronger cryptographic invariants, stricter binary framing, and robust error handling. However, because browsers, reverse proxies, and backend microservices upgrade at different cadences, modern web specifications retain backward-compatibility negotiation mechanisms. 

When protocol version negotiation is not strictly enforced end-to-end, an on-path attacker (or an attacker exploiting gateway misconfiguration) can intercept negotiation probes, force ALPN fallbacks, and downgrade communicating web endpoints to legacy, vulnerable protocol versions (e.g., forcing HTTP/1.0 to bypass chunked validation or forcing cleartext HTTP/1.1).

```
Client (Supports HTTP/2, HTTP/1.1)            Reverse Proxy Gateway                    Backend Web Server
      │                                                │                                       │
      ├─── TLS ClientHello [ALPN: h2, http/1.1] ──────>│                                       │
      │                                                │                                       │
[ Gateway strips h2 from ALPN negotiation ]            │                                       │
      │<── ServerHello [ALPN Selected: http/1.1] ──────┤                                       │
      │                                                │                                       │
(Downgraded to HTTP/1.1 cleartext backend!)            │                                       │
      ├─── POST /api/transfer HTTP/1.1 ───────────────>├──── POST /api/transfer HTTP/1.1 ─────>│
      │    [Ambiguous Transfer-Encoding injected]      │     [Smuggling Exploit Triggered!]    │
      ═════════════════════════════════════════════════╧═══════════════════════════════════════
      CONNECTION DOWNGRADED TO LEGACY PARSER! (Request Smuggling Primitives Re-Enabled!)
```

#### Notable Web Downgrade Precedents
1. **HTTP/1.0 Fallback to Evade Host Header Validation**: RFC 1945 (HTTP/1.0) did not mandate the `Host` header. Sending an HTTP/1.0 request line to a modern reverse proxy frequently forces the proxy into a legacy virtual-host resolution mode, enabling Host Header Injection and routing bypasses.
2. **HTTP/0.9 Response Splitting**: Legacy web servers still tolerating HTTP/0.9 (single-line `GET /path\r\n` with no headers) omit response headers entirely, allowing attackers to inject raw HTML directly into browser contexts without Content-Type or CSP enforcement.
3. **ALPN Downgrade in Gateway Translation**: Stripping HTTP/2 ALPN tokens forces connections to HTTP/1.1, opening the door to classical CL.TE and TE.CL request smuggling.

#### Step-by-Step Audit Workflow
1. Map the protocol version negotiation lineage (e.g., HTTP/0.9 $\to$ HTTP/1.0 $\to$ HTTP/1.1 $\to$ HTTP/2 $\to$ HTTP/3).
2. Test whether the web server responds to legacy HTTP/1.0 request lines (`GET / HTTP/1.0\r\n\r\n`).
3. Check whether the server enforces the `Host` header on HTTP/1.0 requests (RFC 9112 mandates rejecting missing Host on HTTP/1.1, but legacy branches often omit this check for HTTP/1.0).
4. Evaluate which security headers (CSP, HSTS, SameSite, Secure flags) are dropped during protocol downgrade.

---

### 7.2 Vector 2: Deprecated Web Feature Persistence

#### Theoretical Premise & Architecture
When the IETF HTTP Working Group deprecates an insecure web feature, web server maintainers face a commercial dilemma: disabling the feature breaks legacy enterprise web clients. Consequently, web server codebases retain deprecated features behind undocumented configuration flags, fallback routines, or unmaintained parser branches.

These **Zombie Code Paths** represent prime attack surface: they are completely ignored during modern security reviews, receive zero bug fixes, yet remain reachable via crafted HTTP requests.

```
Incoming Request: "TRACE /api/v1/user HTTP/1.1\r\nHost: example.com\r\n..."
                      │
                      ▼
         [ Modern Edge Reverse Proxy ]
         - Configured for standard REST: GET, POST, PUT, DELETE, PATCH
         - Unknown/Deprecated method handling: PASS-THROUGH
                      │
                      ▼
         [ Legacy Backend / Apache Server ]
         - RFC 2616 §9.8 TRACE enabled by default
         - Echoes complete incoming HTTP request including HttpOnly Cookies!
                      │
                      ▼
         [ Reflected Response Exfiltrates HttpOnly Auth Tokens via XST! ]
```

#### Notable Deprecated Web Feature Surfaces
- **HTTP `TRACE` Method (RFC 2616 §9.8)**: Designed for diagnostic debugging; echoes the exact received request back to the client. Exploited via Cross-Site Tracing (XST) to steal `HttpOnly` session cookies otherwise inaccessible to JavaScript.
- **Obsolete Line Folding (`obs-fold` - RFC 7230 §3.2.4)**: Deprecated multi-line headers (`Header: Val1\r\n Val2`). Passing folded headers through a modern reverse proxy to a legacy backend allows attackers to inject smuggled headers into downstream requests.
- **Transfer-Encoding: identity (RFC 2616 §14.41)**: Explicitly deleted in RFC 7230 §3.3.1. Servers built on legacy specifications treat it as a valid non-chunked body, while modern proxies reject or ignore it, causing CL vs. TE desynchronization.
- **Multipart Range Requests (`Range: bytes=0-1,2-3...`)**: Obsoleted denial-of-service vector ("Apache Killer" CVE-2011-3192). Requesting thousands of overlapping byte ranges forces web servers to allocate massive multipart response structures, exhausting CPU and RAM.

---

### 7.3 Vector 3: Web Specification Ambiguity Exploitation (Parser Differentials)

#### Theoretical Premise & Architecture
When a Web RFC uses normative `SHOULD` or `MAY` statements regarding syntax interpretation, or provides an underspecified ABNF grammar, independent software teams implement divergent parsing algorithms. In a multi-tier environment, if the frontend reverse proxy and the backend application server parse the same message through different grammars, the security boundaries collapse.

```
Crafted HTTP Request Stream:
-------------------------------------------------------------
POST / HTTP/1.1\r\n
Host: victim.com\r\n
Content-Length: 13\r\n
Transfer-Encoding: chunked\r\n
\r\n
0\r\n
\r\n
GPOST /admin HTTP/1.1\r\n   <-- Smuggled Request Prefix!
Host: victim.com\r\n
...
-------------------------------------------------------------

Frontend Proxy (Prioritizes Content-Length: 13 bytes):
- Reads exactly 13 bytes: "0\r\n\r\n" + start of next block.
- Forwards entire block as a SINGLE request.

Backend Server (Prioritizes Transfer-Encoding: chunked):
- Reads chunk of size 0: Marks Request 1 COMPLETE!
- Remaining octets "GPOST /admin..." are treated as the START OF REQUEST 2!
- Next victim user's request is appended to "GPOST /admin...", executing unauthorized admin actions!
```

#### Taxonomy of Web Parser Differentials
1. **Delimiter Differentials**:
   - `CRLF` vs. bare `LF` vs. bare `CR` line termination.
   - Semicolon (`;`) vs. Ampersand (`&`) query parameter delimiters.
   - Forward slash (`/`) vs. Backslash (`\`) vs. Semicolon (`;`) in URL paths.
2. **Duplicate Header Semantics**:
   - When a header appears twice (`Host: a.com` and `Host: b.com`):
     - *First-Wins*: Nginx, Cloudflare.
     - *Last-Wins*: Apache Traffic Server, Node.js.
     - *Concatenate with Comma*: Go `net/http`, RFC 9110 mandate (`Host: a.com, b.com`).
     - *Reject with 400*: Envoy, HAProxy, RFC 9112 strict mode.
3. **Whitespace Tolerance Differentials**:
   - Spaces before colons: `Header : Value` (RFC 9112 §5.1 strictly forbids; older servers strip the space and accept).
   - Tabs within header values: `Transfer-Encoding:[\t]chunked`.
   - Obsolete control characters: Form-feed (`0x0C`), Vertical Tab (`0x0B`), Non-breaking space (`0xA0`).
4. **Encoding & Case Sensitivity**:
   - `Transfer-Encoding: Chunked` (Title case).
   - `Transfer-Encoding: "chunked"` (Quoted string).
   - URL-encoded method names: `%50OST / HTTP/1.1`.

---

### 7.4 Vector 4: Web RFC Version Conflict Analysis

#### Theoretical Premise & Architecture
Modern enterprise web infrastructure routinely chains together software stacks developed across three different decades:
- **Cloud CDN**: Implements **RFC 9112** (HTTP/1.1, published 2022).
- **Reverse Proxy**: Implements **RFC 7230** (HTTP/1.1, published 2014).
- **Internal Legacy Microservice**: Implements **RFC 2616** (HTTP/1.1, published 1999).

When rules governing message validation change between RFC revisions, passing an HTTP message across this heterogeneous chain causes an **Inter-Generational Impedance Mismatch**.

```
Version Conflict Case: Header Line Folding (obs-fold)

[ Client ] 
    │ Transmits: "Header: Value1\r\n\tValue2"
    ▼
[ CDN (RFC 9112) ] 
    - Mandate: "A server MUST reject with 400 (Bad Request)..."
    - CDN evaluates: PASS-THROUGH (Configured as transparent proxy)
    ▼
[ Reverse Proxy (RFC 7230) ]
    - Mandate: "Proxies SHOULD replace obs-fold with SP..."
    - Proxy transforms: "Header: Value1 Value2"
    - Security Filter applies WAF rules against transformed string!
    ▼
[ Backend Microservice (RFC 2616) ]
    - Legacy parser splits on raw CRLF!
    - Second line "\tValue2" treated as independent smuggled header!
```

#### Key Historical Inter-Generational Conflicts in Web RFCs
- **Transfer-Encoding: identity**: RFC 2616 §14.41 permitted `identity`. RFC 7230 §3.3.1 explicitly deleted `identity`. Servers following 2616 treat it as valid non-chunked body; servers following 7230 reject or treat as unknown.
- **Multiple Content-Length Values**: RFC 2616 was silent on duplicate identical `Content-Length: 5\r\nContent-Length: 5`. RFC 7230 §3.3.2 mandated rejecting multiple Content-Lengths unless values were identical; RFC 9112 §6.3 mandates rejecting ALL requests with multiple Content-Length headers regardless of values.
- **URI Path Normalization**: RFC 2396 vs. RFC 3986 vs. WHATWG URL Standard handle dot-segments (`/a/b/../c`) and percent-encoding normalization with fundamentally conflicting algorithms.

---

### 7.5 Vector 5: Web State Machine Discrepancy Analysis

#### Theoretical Premise & Architecture
Complex web protocols maintain connection and stream state across a sequence of frame exchanges. A web state machine vulnerability exists when:
1. An implementation permits transitions between states that are logically illegal in the specification.
2. The specification leaves error states or resource cleanup undefined.
3. An attacker can interleave inputs across concurrent execution threads to corrupt transactional state.

```
HTTP/2 Stream State Machine (RFC 7540 / RFC 9113 §5.1)

                          +--------+
                  send PP |        | recv PP
                 ,--------|  idle  |--------.
                /         |        |         \
               v          +--------+          v
        +----------+                      +----------+
        |          |                      |          |
        | reserved |                      | reserved |
        | (local)  |                      | (remote) |
        |          |                      |          |
        +----------+                      +----------+
             \                                  /
      recv H  \--------.              .--------/ send H
                        v            v
                          +--------+
                          |  open  |  <--- ATTACK ZONE: Stream Cancellation Flood
                          +--------+
                         /          \
               send ES  /            \  recv ES
                       v              v
              +----------+          +----------+
              |   half   |          |   half   |
              |  closed  |          |  closed  |
              | (remote) |          | (local)  |
              +----------+          +----------+
                       \              /
                recv ES \            / send ES
                         v          v
                          +--------+
                          | closed |  <--- Rapid Reset Target (RST_STREAM)
                          +--------+
```

#### Concrete Web State Machine Attack Classes
- **HTTP/2 Rapid Reset (CVE-2023-44487)**: RFC 7540 permits a client to open a stream (`HEADERS`) and immediately cancel it (`RST_STREAM`). Because the specification did not mandate transaction costs or rate limits on stream cancellations, attackers forced servers to allocate expensive server-side request contexts and immediately tear them down, generating catastrophic 398M rps DDoS attacks.
- **CONTINUATION Frame Flood (CVE-2024-27983)**: RFC 7540 §6.10 mandates that an uncompleted `HEADERS` frame block MUST be followed by `CONTINUATION` frames until the `END_HEADERS` flag is set. Because the state machine forbids any other frames on the connection while awaiting `CONTINUATION`, servers buffered endless header blocks in memory without processing, crashing major HTTP/2 runtimes.
- **Pipelining Desynchronization**: Sending a pipelined request immediately following a request awaiting `100-continue`. Web servers processing the pipeline prematurely bind the second request to the uncompleted state of the first.

---

### 7.6 Vector 6: Web Extension Mechanism Abuse

#### Theoretical Premise & Architecture
To remain extensible, Web RFCs define extension points: custom HTTP methods, arbitrary header fields, and connection options. When intermediate reverse proxies encounter an extension they do not understand, specifications mandate that they **MUST ignore and pass through** the unknown token.

Attackers abuse extension points to smuggle instructions past perimeter defenses or manipulate the internal trust chain:

```
Perimeter WAF / Reverse Proxy            Internal Microservice Gateway
      │                                                │
      ├─── GET /admin HTTP/1.1 ───────────────────────>│
      │    Connection: close, X-Forwarded-For          │
      │    X-Forwarded-For: 127.0.0.1                  │
      │                                                │
[ WAF processes request ]                              │
- Validates client IP via X-Forwarded-For             │
- Forwards request downstream                          │
- Obeys RFC 7230 Hop-by-Hop rule:                      │
  "Connection header lists fields to be STRIPPED!"     │
  WAF strips X-Forwarded-For before forwarding! ──────>│
                                                       │
                                  [ Internal Gateway receives NO client IP! ]
                                  - Falls back to default trust: ALLOW ADMIN!
```

#### 7.6.1 The Hop-by-Hop Header Stripping Grammar & Reverse Proxy Exploitation

RFC 7230 §6.1 (and RFC 9110 §7.6.1) defines the formal grammar for connection management:
```abnf
Connection        = *( "," OWS ) connection-option *( OWS "," [ OWS connection-option ] )
connection-option = token
```

The specification dictates: *"Any header field whose name matches one of the connection-options MUST be removed by the proxy before forwarding the message."*

##### The Reverse Proxy Strip Attack Mechanics
In a multi-tier proxy architecture where the edge reverse proxy authenticates client identity and passes security context downstream via custom HTTP headers:
- `X-Forwarded-For`: Contains client's true public IP address.
- `X-Client-SSL-Cert`: Contains verified mTLS client identity.
- `X-Custom-Auth-User`: Contains authenticated username from the identity provider.

```http
GET /admin HTTP/1.1
Host: edge.example.com
Connection: close, X-Forwarded-For, X-Client-SSL-Cert
X-Forwarded-For: 198.51.100.2
X-Client-SSL-Cert: InvalidSignature
```

When the edge proxy processes the request:
1. It validates the client against edge policies.
2. Prior to forwarding the request to the internal backend, it executes the RFC 7230 §6.1 Hop-by-Hop cleanup loop: it reads `Connection`, extracts tokens `X-Forwarded-For` and `X-Client-SSL-Cert`, and **strips both headers** from the outgoing request!
3. The internal backend receives the request with NO client IP and NO client certificate.
4. The internal backend evaluates the missing headers against fallback logic (e.g., assuming localhost connections or default service accounts), granting unauthenticated access!

---

### 7.7 Vector 7: Cross-Protocol Boundary Analysis in Web Gateways

#### Theoretical Premise & Architecture
Modern cloud web architectures rely heavily on **Protocol Translation Gateways**:
- Frontend clients speak **HTTP/2** or **HTTP/3** over TLS.
- Edge gateways terminate HTTP/2 and downgrade traffic to **HTTP/1.1 cleartext** over internal TCP connections.
- Specialized proxies translate between **gRPC-Web** and binary **gRPC/HTTP/2**, or **WebSocket** and raw TCP.

When an outer protocol with rich structural framing (e.g., binary frames, length-delimited fields) is translated into an inner protocol with primitive ASCII delimiters (e.g., CRLF-separated text), **Cross-Protocol Smuggling** occurs.

```
Binary Protocol (HTTP/2)                  Gateway Translation               Text Protocol (HTTP/1.1)
:method = POST                            Translates binary               POST /api/v1/update HTTP/1.1\r\n
:path = /api/v1/update                    pseudo-headers into             Host: victim.com\r\n
:authority = victim.com                   ASCII request lines             Foo: Bar\r\n\r\n
custom-header = "Bar\r\n\r\nSMUGGLED"   ────────────────────────────>   SMUGGLED /admin HTTP/1.1\r\n...
                                                                          \_________________________/
(H2 binary allows literal \r\n in values!)                                 Injected into HTTP/1.1 stream!
```

#### Notable Cross-Protocol Web Vulnerabilities
- **H2.CL and H2.TE Smuggling (RFC 9113 §8.2)**: HTTP/2 does not require `Content-Length` or `Transfer-Encoding`. However, when a gateway translates an HTTP/2 request to HTTP/1.1, it must synthesize these headers. Injecting a forged `content-length` header into an HTTP/2 frame causes the backend HTTP/1.1 server to desynchronize message boundaries.
- **Pseudo-Header CRLF Injection**: Injecting newline characters into binary HTTP/2 `:path` or `:authority` pseudo-headers. When written to the raw HTTP/1.1 socket, the newline terminates the request line early, injecting arbitrary headers into the backend request stream.
- **WebSocket Tunnel Desync (RFC 6455 §4)**: Reverse proxy forwards an upgrade request and switches to a transparent byte-pipe, but the backend rejects the upgrade. Pipelined HTTP requests inside the payload stream are executed as internal unauthenticated requests.

---

### 7.8 Vector 8: Web Error Handling Divergence

#### Theoretical Premise & Architecture
Web RFCs exhaustively detail the "happy path," but frequently treat error handling as an afterthought, using phrases such as *"the response is implementation-defined"* or *"servers MAY close the connection"*.

When a web server encounters malformed input, it must decide:
1. **Fail-Open vs. Fail-Close**: Does the web server reject the request, or does it attempt to strip the malformed byte and continue processing?
2. **Partial Execution Before Abort (Shotgun Parsing)**: Did the application execute persistent database mutations before the parser reached the malformed byte and returned `400 Bad Request`?
3. **Pipeline Drain Failure**: When returning `400 Bad Request`, does the server read and discard the remaining bytes declared by `Content-Length`, or does it leave them on the persistent TCP socket?

#### 7.8.1 Pipeline Desynchronization via Unconsumed Error Request Bodies

When an HTTP/1.1 backend server encounters an error during header processing or early payload inspection (e.g., invalid JSON syntax, unauthorized token, or rate limit threshold reached), it generates an error response (such as `400 Bad Request` or `401 Unauthorized`).

##### The Pipeline Poisoning Failure Mode
If the client's request declared a large `Content-Length` (e.g., `Content-Length: 10000`), the backend server must decide how to handle the unconsumed body octets still arriving over the persistent TCP socket:

```
RFC 9112 §9.3 Requirement:
"If a server receives a request that it does not want to read to completion,
 it MUST close the connection after sending the response."
```

Many high-performance web servers (e.g., custom Go or Node.js microservices) attempt to keep the persistent connection alive to avoid TCP handshake overhead:
1. The server emits `HTTP/1.1 400 Bad Request` with `Connection: keep-alive`.
2. The server **fails to read and discard the 10,000 unconsumed payload bytes** remaining in the socket receive buffer.
3. When the next request arrives on that persistent connection (either from the same client or a subsequent user multiplexed over a shared reverse proxy pool), the server's read loop reads the **unconsumed payload bytes from Request 1**, treating them as the start of Request 2!
4. The subsequent request's headers and session cookies are appended to the attacker's leftover payload, causing complete session hijacking or request execution desynchronization!

---

