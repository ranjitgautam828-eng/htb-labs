# Machine: Fawn
- **Environment:** HTB Lab
- **Difficulty:** Very Easy
- **Tags:** FTP, Anonymous Access, Misconfiguration, File Exposure, Linux
---
 
## 1. Executive Summary
 
- **Vulnerability:** Anonymous FTP misconfiguration allowing unauthenticated access
- **Entry vector:** Public FTP login with anonymous credentials
- **Exploit result:** Direct file exposure and retrieval of sensitive artifact
- **Root cause:** Disabled authentication boundary on file transfer service
- **Real-world equivalent:** Exposed enterprise FTP server leaking internal data
---
 
## 2. Attack Path (Decision Chain ONLY)
 
**Decision →** Identify FTP service exposure
**Why:** Open port indicates file transfer surface
**Outcome:** Target class confirmed
**Learning:** Service enumeration defines attack surface
 
**Decision →** Test authentication boundary via default access mode
**Why:** FTP commonly supports anonymous login — primary failure vector in legacy services
**Outcome:** Authentication bypass succeeds
**Learning:** Default credential logic is the first test against any legacy file service
 
**Decision →** Enumerate exposed filesystem via directory listing
**Why:** Anonymous access grants unauthenticated directory visibility
**Outcome:** Sensitive file discovered in root directory
**Learning:** Unauthenticated enumeration immediately reveals extraction path — no exploit needed
 
**Decision →** Extract file using transfer primitive
**Why:** FTP separates execution from transfer layer — no shell required
**Outcome:** Local copy of target artifact obtained
**Learning:** File transfer protocol bypasses shell execution requirement entirely — extraction IS the exploit
 
---
 
## 3. High-Value Intelligence Layer
 
**Recon**
- File-transfer service identified as primary attack surface with no authentication gating at protocol level
**Enumeration**
- Anonymous access granted unrestricted directory visibility; sensitive file in root directory amplified exposure severity
**Root Cause**
- FTP daemon configured with anonymous auth enabled, no directory isolation, and sensitive artifacts stored in publicly reachable namespace
**Security Fix**
- Disable anonymous FTP; enforce authenticated sessions with chroot isolation on all shared directories
---
 
## 4. Pattern Recognition Engine
 
**Pattern: Anonymous Service Trust Failure**
- **Signal:** Unauthenticated login accepted without credential validation
- **Trigger:** Successful login with default anonymous identity
- **Shortcut:** If service allows anonymous entry → assume full filesystem enumeration risk; skip to extraction
- **Class:** FTP anonymous, Redis no-auth, MongoDB no-auth, SMB guest, LDAP null bind, Elasticsearch open HTTP
**Pattern: File Exposure Without Exploit Requirement**
- **Signal:** Sensitive file visible without privilege escalation
- **Trigger:** Directory listing returns high-value artifact pre-auth
- **Shortcut:** If file is readable pre-auth → skip exploitation phase entirely, proceed directly to extraction
---
 
## 5. Defensive Insight (Blue Team Mapping)
 
- **Fix:** Disable anonymous authentication on FTP service; enforce credential-gated sessions only
- **Control:** RBAC + chroot isolation on all file transfer services; remove sensitive artifacts from public namespaces
- **Detection:** Alert on login events where username field is `anonymous` or `ftp` AND a file transfer (`RETR`) completes within the same session
---
 
## 6. Mistakes & Corrections
 
- **Mistake:** Attempting shell-based commands inside FTP session (e.g., `cat`)
- **Cause:** Misclassification of protocol capability — FTP is transfer-only, not execution environment
- **Fix:** Use `get` / `put` exclusively inside FTP sessions
- **Future rule:** FTP = transfer interface only; never attempt shell execution within protocol boundary
 