---
name: b63-patch-diff-analysis
description: "Use when analyzing a security patch/commit to find the vuln."
version: 1.0.0
author: b63
platforms: [linux, macos]
metadata:
  hermes:
    tags: [patch, diff, git, code-analysis, vulnerability-research]
    category: b63
    related_skills: [b63-agent, b63-cve-research, b63-exploit-dev]
---

# b63 Patch-Diff Analysis

Methodology for analyzing security patches to understand what was vulnerable, why, and how to trigger it.

## When to Use

- Given a fixing commit and need to understand the pre-patch vulnerability
- Reverse-engineering a vulnerability from its patch (1-day analysis)
- Mapping a patch to exploitable conditions
- Determining if a patch is complete or leaves residual attack surface

## Input Contract (Expected from b63-cve-research)

This skill expects the following data from the preceding CVE research phase:
- **Fix commit hash or link** (required -- the central artifact)
- **Repository URL** (required -- where to clone/fetch)
- **CVE ID** (if assigned)
- **Vulnerability class** (CWE or plain description)
- **Affected component** (file path or module name, if known from advisory)
- **Affected version range** (to scope the checkout)

If these are not available, this skill will attempt to locate them autonomously, but starting with a research summary accelerates the analysis significantly.

## Core Principle

A security patch is an exploit roadmap read backward. The patch shows exactly:
- What check was missing (the vulnerability)
- What input reaches the vulnerable point (the trigger)
- What happens without the check (the impact)

## Step 1: Obtain the Diff

### From a commit hash
```bash
git show <commit-hash> --stat          # overview: which files changed
git show <commit-hash> -p              # full patch
git show <commit-hash> -- path/to/file # specific file
```

### From a version range
```bash
git log --oneline v1.0..v1.1 -- path/to/file
git diff v1.0..v1.1 -- path/to/file
```

### From a patch file
```bash
# Apply to see context
git apply --stat patch.diff
git apply --check patch.diff
```

## Step 2: Classify the Change

Read the diff and categorize what was added/removed:

| Pattern in Diff | Likely Vulnerability Class |
|-----------------|--------------------------|
| Added bounds/length check before memcpy/strcpy | Buffer overflow |
| Added NULL check before dereference | NULL pointer deref / UAF |
| Added free() tracking or refcount | Use-after-free |
| Added input validation/sanitization | Injection (SQL/cmd/template) |
| Added authentication/authorization check | Auth bypass / privilege escalation |
| Changed comparison (== to ===, timing-safe) | Type juggling / timing side-channel |
| Added URL/protocol validation | SSRF |
| Replaced eval/deserialize with safe alternative | Code injection / insecure deserialization |
| Added integer overflow check | Integer overflow leading to memory corruption |
| Added rate limiting / size cap | DoS |

## Step 3: Map the Vulnerable Path

Work backward from the fix:

1. **Identify the patched function(s)** -- what functions were modified?
2. **Find callers** -- who calls these functions? (`git grep`, cscope, ctags)
3. **Trace the input** -- how does external data reach the vulnerable point?
4. **Identify the control point** -- what does the attacker control (length, content, type)?
5. **Determine reachability** -- what preconditions must hold for the vulnerable path to execute?

### Call-chain template
```
[Entry Point] -> [Parser/Handler] -> [Vulnerable Function]
     |                |                      |
  (attacker input)  (partial validation?)  (missing check patched here)
```

## Step 4: Analyze Patch Completeness

Questions to answer:
- Does the fix address the root cause or just one symptom?
- Are there other callers of the vulnerable function that remain unpatched?
- Is the new validation sufficient? (e.g., length check but not content check)
- Are there integer overflow possibilities in the new bounds check itself?
- Could the check be bypassed via a different code path?

### Common incomplete-fix patterns
- Fix in one caller but the library function is still unsafe for other callers
- Blocklist approach (blocks known-bad) instead of allowlist (permits known-good)
- Check on one field but the same vulnerability class exists in adjacent fields
- Fix in the main path but not the error-handling path

## Step 5: Reconstruct Pre-Patch State

```bash
# Check out the parent commit (state before the fix)
git checkout <commit-hash>~1

# Or create a branch at the vulnerable state
git checkout -b vuln-analysis <commit-hash>~1

# Build the vulnerable version for testing
# (project-specific -- check build docs)
```

## Step 6: Document the Analysis

Produce:

```
Patch Analysis: [commit hash]
==============================
Fixed vulnerability: [CVE if known]
Component:          [file:function]
Change summary:     [one line: what the patch adds/removes]

Pre-patch state:
  - [description of vulnerable code behavior]
  - [what input triggers it]
  - [what happens -- the primitive gained]

Trigger conditions:
  - [required auth/config/state]
  - [input format/constraints]
  - [timing/race requirements if any]

Patch completeness:
  - [complete/partial]
  - [residual attack surface if partial]

Exploitation path:
  - [how to go from trigger to impact]
  - [what primitives are available: read/write/control-flow]
```

## Pitfalls

- Do not assume the commit message accurately describes the security impact -- read the code
- A "cleanup" or "refactoring" commit may silently fix a security bug
- Multi-file patches may have the security fix mixed with unrelated changes -- isolate the security-relevant hunks
- The fix may reference a separate test file that demonstrates the trigger
- Some patches are backports -- check if the original commit has more context
- A revert of a revert can obscure the actual vulnerability introduction point
