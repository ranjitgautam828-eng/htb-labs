# Machine: Dancing

- **Environment:** HTB Starting Point
- **Difficulty:** Very Easy
- **Tags:** SMB, Windows, Anonymous Access, File Exposure
---
 
## 1. Executive Summary
 
- **Vulnerability:** SMB anonymous share exposure with no authentication enforcement
- **Entry vector:** Unauthenticated SMB session with guest-level share enumeration
- **Exploit result:** Sensitive file retrieved directly from user share namespace
- **Root cause:** Guest access enabled on SMB with no ACL enforcement on user directories
- **Real-world equivalent:** Exposed internal Windows file share leaking user data across enterprise network
---
 
## 2. Attack Path (Decision Chain ONLY)
 
**Decision →** SMB service exposure detected
**Why:** Open SMB port on Windows target indicates file-sharing attack surface
**Outcome:** Attack surface confirmed as share enumeration, not exploit development
**Learning:** Windows + SMB = test anonymous access before any other vector
 
**Decision →** Anonymous session attempted against SMB
**Why:** SMB guest access is a common misconfiguration in Windows environments
**Outcome:** Authentication boundary bypassed — share listing obtained without credentials
**Learning:** Null/guest auth is the first test against any SMB service; success collapses the entire auth model
 
**Decision →** Shares classified as SYSTEM vs USER
**Why:** System shares (IPC$, ADMIN$, C$) are administrative — user shares carry actual data
**Outcome:** WorkShares identified as the only non-default, user-controlled share
**Learning:** Deprioritize default shares immediately; custom share names are the real target
 
**Decision →** User directory structure enumerated within WorkShares
**Why:** Decentralized user directories in SMB shares imply personal file storage
**Outcome:** Sensitive artifact located in user subfolder
**Learning:** In SMB environments, user-named directories are primary data exfiltration targets
 
**Decision →** File extracted via SMB transfer primitive
**Why:** Share access grants direct file retrieval — no shell or privilege escalation required
**Outcome:** Flag obtained
**Learning:** Unauthenticated share access = direct exfiltration path; exploitation phase unnecessary
 
---
 
## 3. High-Value Intelligence Layer
 
**Recon**
- Windows SMB exposure indicated file-sharing surface; authentication absence eliminated exploit development phase entirely
**Enumeration**
- SYSTEM vs USER share separation revealed standard Windows SMB layout; user share namespace confirmed as primary data exposure point
**Root Cause**
- SMB guest access enabled with no ACL enforcement on user directories; sensitive files stored directly in accessible share namespace — three independent control failures required to reach this state
**Security Fix**
- Disable anonymous/guest SMB sessions; enforce NTLM or Kerberos authentication with explicit ACLs per share; remove sensitive files from any guest-readable namespace
---
 
## 4. Pattern Recognition Engine
 
**Pattern: SMB Guest Share Exposure**
- **Signal:** SMB service reachable without credential challenge
- **Trigger:** Anonymous session returns share listing
- **Shortcut:** Windows + open SMB → test null/guest auth before any exploit path; success means the authentication model is already collapsed
- **Class:** SMB guest, FTP anonymous, NFS no-auth, Redis no-auth, LDAP null bind
**Pattern: User-Directory Data Leakage via Share Access**
- **Signal:** Multiple user-named directories visible inside accessible share
- **Trigger:** Sensitive file appears directly inside user subfolder with no additional auth gate
- **Shortcut:** In SMB environments, classify shares as SYSTEM vs USER immediately — user shares are always the primary exfiltration target
---
 
## 5. Defensive Insight (Blue Team Mapping)
 
- **Fix:** Disable anonymous and guest SMB sessions; enforce authenticated access (NTLM/Kerberos) on all shares
- **Control:** Per-share ACL hardening with explicit user/group allow-lists; network segmentation isolating file services from general access
- **Detection:** Alert on Windows Event ID 4624 (anonymous logon type 3) + subsequent SMB share enumeration (`IPC$` access followed by rapid `TREE CONNECT` requests across multiple shares within same session)
---
 
## 6. Mistakes & Corrections
 
- **Mistake:** Prioritized system shares (ADMIN$, C$, IPC$) before user shares
- **Cause:** Over-indexing on share count and visibility instead of access logic — default shares rarely contain user data
- **Fix:** Immediately classify shares into SYSTEM vs USER on discovery; enumerate user shares first
- **Future rule:** Custom share names = user data; default administrative share names = infrastructure noise; always enumerate in that order