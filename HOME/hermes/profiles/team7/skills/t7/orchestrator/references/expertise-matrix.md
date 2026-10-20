# team7 Comprehensive Expertise Matrix

Moved verbatim from SOUL.md on 2026-10-19 (slim-down: Hermes keeps only the first 168,000 + last 48,000 chars of SOUL.md in the system prompt; this block lived in the omitted middle). Load via skill_view(name="t7-orchestrator") or read directly when needed.

---

## COMPREHENSIVE EXPERTISE MATRIX

### 1. WEB APPLICATION SECURITY (GRANDMASTER LEVEL)

#### OWASP Top 10 (2021) - Deep Exploitation

**A01: Broken Access Control**
```
Attack Vectors:
- IDOR (Insecure Direct Object References)
  - Numeric ID manipulation
  - UUID/GUID prediction
  - Hash collision exploitation
  - Parameter pollution for access bypass
- Privilege Escalation
  - Horizontal (user-to-user)
  - Vertical (user-to-admin)
  - Role manipulation
  - JWT claim tampering
- Path Traversal
  - Classic ../ sequences
  - URL encoding bypasses (%2e%2e%2f)
  - Double URL encoding
  - UTF-8 encoding attacks
  - Null byte injection
- Forced Browsing
  - Admin panel discovery
  - Backup file exposure
  - Debug endpoint access
  - API versioning exploitation
- CORS Misconfiguration
  - Wildcard origin reflection
  - Null origin exploitation
  - Subdomain takeover chains
  - Pre-flight bypass techniques
```

#### Tactical Field Notes
**Privileged Action Exploitation**
- **Observation**: Administrative or privileged actions (like deleting a case) might be available to specific users but hidden from the UI until logged in as that user.
- **Tactic**: Once a privileged account is compromised (e.g., via IDOR-leaked credentials), explore all available dashboards and menus for new functionality that was previously inaccessible. Look for buttons, forms, or API calls that perform state-changing actions.

**HTML Entity Encoding in Passwords**
- **Observation**: Passwords retrieved from APIs or databases might contain HTML entities (e.g., `&#2a` for `*`).
- **Tactic**: If a password retrieved from a JSON response or HTML page fails to work, check for HTML entities and decode them (e.g., `&amp;` -> `&`, `&#2a` -> `*`) before using them in login forms.

### 2. NETWORK SECURITY & INFRASTRUCTURE (GRANDMASTER LEVEL)

#### Network Reconnaissance

**Passive Reconnaissance**
```bash
# DNS Intelligence
dig +short target.com ANY
dig +short -x $(dig +short target.com)
fierce --domain target.com
dnsenum target.com
dnsrecon -d target.com -t std,brt,srv,axfr

# Certificate Transparency
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r ".[].name_value" | sort -u

# Subdomain Enumeration
subfinder -d target.com -all -silent
amass enum -passive -d target.com
assetfinder --subs-only target.com
github-subdomains -d target.com -t $GITHUB_TOKEN
```

**Active Reconnaissance**
```bash
# Advanced Nmap Scanning
nmap -sS -sV -sC -O -A -p- --script=vuln,exploit,auth,default -oA full_scan target.com
nmap -sU -sV --top-ports 1000 -oA udp_scan target.com
nmap --script=http-enum,http-vuln*,http-sql-injection target.com
```

### 3. ACTIVE DIRECTORY DOMINATION (GRANDMASTER LEVEL)

#### AD Reconnaissance
```powershell
# PowerView Reconnaissance
Import-Module PowerView.ps1
Get-Domain
Get-DomainController
Get-DomainUser -Properties samaccountname,description
Get-DomainGroup -AdminCount
Get-DomainGroupMember "Domain Admins"
Get-DomainComputer -Properties dnshostname,operatingsystem
Find-LocalAdminAccess
Get-DomainTrust
Get-ForestTrust

# BloodHound Collection
SharpHound.exe -c All,GPOLocalGroup --outputdirectory C:\temp
bloodhound-python -d target.local -u user -p pass -c All
```

### 4. CLOUD SECURITY EXPLOITATION (GRANDMASTER LEVEL)

#### AWS Exploitation
```bash
# IAM Enumeration
aws sts get-caller-identity
aws iam list-users
aws iam list-roles
aws iam list-attached-user-policies --user-name target
aws iam get-policy-version --policy-arn <arn> --version-id v1

# EC2 Metadata Exploitation (SSRF)
curl http://169.254.169.254/latest/meta-data/
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
curl http://169.254.169.254/latest/user-data
```

### 5. PRIVILEGE ESCALATION ENCYCLOPEDIA

#### Linux Privilege Escalation
```bash
# Automated Enumeration
./linpeas.sh
./linux-exploit-suggester.sh
./LinEnum.sh

# SUID/SGID Binaries
find / -perm -4000 -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null

# Capabilities
getcap -r / 2>/dev/null
```

#### Windows Privilege Escalation
```powershell
# Automated Enumeration
.\winPEAS.exe
.\PowerUp.ps1
Invoke-AllChecks
.\Seatbelt.exe -group=all
```

---
