## 8. Web Vulnerability Chaining & Attack Graph Synthesis

Isolated web specification ambiguities are frequently dismissed by security triage teams as "low-severity conformance quirks." The true power of Web-RVRM lies in **Attack Graph Synthesis**: composing multiple orthogonal web specification flaws into high-impact, critical exploit chains.

```
+-------------------------------------------------------------------------+
| GRAPH-THEORETIC WEB ATTACK CHAIN SYNTHESIS                              |
|                                                                         |
|  [ Ingress Request ] ──> Primitive 1: Parser Discrepancy (Vector 3)     |
|                               │                                         |
|                               ▼                                         |
|                          [ State: WAF Security Boundary Bypassed ]      |
|                               │                                         |
|                               ▼                                         |
|                          Primitive 2: Payload Decomposition (Vector 7)  |
|                               │                                         |
|                               ▼                                         |
|                          [ State: Exploit Injected into Database ]      |
|                               │                                         |
|                               ▼                                         |
|                          Primitive 3: Single-Packet Race (Vector 5)     |
|                               │                                         |
|                               ▼                                         |
|  [ Terminal State ] ──> CRITICAL IMPACT: Unauthenticated Admin Takeover |
+-------------------------------------------------------------------------+
```

### 8.1 Graph-Theoretic Web Vulnerability Chaining

Formally, a web attack pipeline is modeled as a **Directed Acyclic Graph (DAG)**:
$$G = (V, E)$$
- **Vertices ($V$)**: Web operational and authorization states:
  $$V = \{ S_{unauth}, S_{bypassed}, S_{desync}, S_{poisoned\_cache}, S_{admin}, S_{rce} \}$$
- **Edges ($E$)**: Web transformation primitives or parser differentials that transition the system between states:
  $$e = (u, v) \in E \quad \text{enabled by HTTP mutation } m \text{ violating Web RFC invariant } \Phi$$

An exploit exists if there is a directed path from initial unauthenticated state $S_{unauth}$ to a terminal compromise state $S_{compromised} \in \{ S_{admin}, S_{rce} \}$.

### 8.2 Web Attack Primitive Taxonomy

Web-RVRM categorizes individual protocol findings into four composable **Web Attack Primitives**:

| Primitive Class | Operational Mechanism in Web Stacks | Web RFC Weakness Category | Input / Output Transformation |
|---|---|---|---|
| **$\Pi_1$: Boundary Desynchronization** | Forces reverse proxy and backend server to disagree on where an HTTP message begins or ends. | Vector 3 (Ambiguity), Vector 7 (Translation) | Input: Stream $x \cdot y$ $\to$ Output: Smuggled HTTP request on pipeline. |
| **$\Pi_2$: Semantic Mutation / Confusion** | Forces web application to interpret the same syntactic body through an unintended schema. | Vector 3 (Delimiters), Vector 4 (Version Conflict) | Input: JSON Patch $\to$ Coerced to Merge Patch $\to$ Field overwrite. |
| **$\Pi_3$: State Race Condition** | Forces non-atomic execution across concurrent threads to corrupt persistent state. | Vector 5 (State Machine) | Input: Synchronized Single-Packet Attack $\to$ Lost update anomaly. |
| **$\Pi_4$: Security Filter Evasion** | Hides malicious payload tokens from intermediate inspection proxies and WAFs. | Vector 6 (Extensions), Vector 2 (Deprecated) | Input: Payload split across patch operations $\to$ Reassembled in DB. |

### 8.3 Common Compositional Web Attack Archetypes

#### Archetype 1: Content-Type Confusion + Vertical Privilege Escalation
- **Component A (AMB-04)**: Edge proxy permits `application/merge-patch+json` while backend processes body as `application/json-patch+json`.
- **Component B (Schema Gap)**: Backend User object contains an internal `roles` array.
- **Combined Impact**: Attacker submits a Merge Patch containing `{"roles": ["admin"]}`. The proxy validates the payload against a standard user schema (ignoring the merge patch syntax). The backend executes JSON Patch evaluation, adding `admin` to the authorization token, achieving **Vertical Privilege Escalation**.

#### Archetype 2: Missing Resource Creation + ACL Bypass
- **Component A (AMB-01)**: Target framework implements PATCH with upsert semantics (creates missing records).
- **Component B (Middleware Architecture)**: Authentication middleware authorizes `POST /api/users` only for admins, but permits `PATCH /api/users/{id}` for standard users to update their own profile.
- **Combined Impact**: Attacker sends `PATCH /api/users/0` (non-existent ID). The middleware sees `PATCH` and checks update permissions (allowed). The backend creates a new root administrative record with ID 0, achieving **Unauthenticated User Creation**.

#### Archetype 3: Payload Decomposition + Persistent WAF Evasion
- **Component A (AMB-11)**: Target supports RFC 6902 JSON Patch array operations.
- **Component B (WAF Signature Engine)**: WAF inspects incoming requests for SQL injection or stored XSS signatures (e.g., `<script>` or `UNION SELECT`).
- **Combined Impact**: Attacker decomposes the payload across three patch operations in a single request:
  ```json
  [
    {"op": "replace", "path": "/bio", "value": "<scr"},
    {"op": "add", "path": "/bio", "value": "ipt>alert(1)</"},
    {"op": "add", "path": "/bio", "value": "script>"}
  ]
  ```
  The WAF evaluates each operation in isolation (none match the signature). The backend applies the patches sequentially, concatenating the fragments in the database into an executable XSS payload.

#### Archetype 4: Web Cache Poisoning + Session Account Takeover
- **Component A (RFC 9111)**: CDN treats `X-Forwarded-Host` as an unkeyed header.
- **Component B (RFC 6265)**: Application reflects the unkeyed host into OAuth redirection links or session initialization scripts.
- **Combined Impact**: The poison script is cached at the CDN edge. When legitimate users navigate to `/login`, their browser loads the script from the attacker's host, transmitting active session cookies and credentials directly to the attacker, achieving **Global Zero-Click Account Takeover**.

### 8.4 Severity Amplification Proofs in Web Exploitation

In web vulnerability triage, compounding multiple lower-tier findings results in a non-linear leap in calculated CVSS severity:

$$\text{Severity}(A \oplus B) > \max(\text{Severity}(A), \text{Severity}(B))$$

```
Individual Finding 1: Medium (CVSS 5.3) - Missing Content-Type enforcement
Individual Finding 2: Medium (CVSS 6.5) - Inadequate ACL scoping on PATCH verbs
           │
           ▼
Compounded Attack Chain: CRITICAL (CVSS 9.8) - Remote Administrative Takeover
```

### 8.5 Algebraic Web Attack Graph Grammar & Concrete Multi-Step Chains

To automate web exploit chaining, Web-RVRM defines a formal **Transition Grammar**:
$$\langle S_{current}, \Pi_k(m) \rangle \longrightarrow S_{next}$$

#### Comprehensive Case Study: The Zero-Trust Triple-Primitive Web Chain

```
Client (Attacker)              Edge Reverse Proxy              API Gateway / WAF              Internal Microservice
      │                                │                               │                                │
      ├── 1. PATCH /api/users/0 ──────>│                               │                                │
      │   (AMB-01: Non-existent ID)    ├─ 2. Inspects Method: PATCH ──>│                                │
      │   [Content-Type: Coerced]      │     ACL: Standard Users OK    ├─ 3. WAF Inspects Body ────────>│
      │   [Payload Decomposed]         │     (Bypasses POST ACL!)      │     No SQLi / XSS found!       │
      │                                │                               │     (AMB-11: Decomposed)       │
      │                                │                               │                                │
      │                                │                               │                                ├─ 4. Upsert Executes!
      │                                │                               │                                │  - Creates user 0
      │                                │                               │                                │  - Reassembles Admin
      │                                │                               │                                │  - AMB-04 Evaluates!
      │                                │                               │                                │
      │<── 200 OK: Admin Created! ─────┴───────────────────────────────┴────────────────────────────────┴─
      ════════════════════════════════════════════════════════════════════════════════════════════════════
      RESULT: COMPLETE UNAUTHENTICATED SYSTEM TAKEOVER VIA 3 COMPOUNDED SPECIFICATION AMBIGUITIES!
```

---

