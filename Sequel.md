# Machine: Sequel
 
- **Environment:** HTB Starting Point
- **Difficulty:** Easy
- **Tags:** MySQL, MariaDB, Misconfiguration, Credential Exposure, Database Pivot, Linux
---
 
## 1. Executive Summary
 
- **Vulnerability:** MariaDB exposed with remote root access enabled and SSL enforcement misconfigured
- **Entry vector:** Direct root-level database authentication after SSL requirement bypassed at client level
- **Exploit result:** Full database access — sensitive flag retrieved from plaintext application config table
- **Root cause:** Remote root login enabled with no privilege separation; secrets stored in queryable config table
- **Real-world equivalent:** Exposed production database with insecure remote admin access and plaintext secrets in application config layer
---
 
## 2. Attack Path (Decision Chain ONLY)
 
**Decision →** Identify single exposed database port — shift entire attack surface to DB-only interaction
**Why:** No web or SSH services present; attack surface collapses to single service class
**Outcome:** Multi-service enumeration eliminated — direct DB interaction becomes only viable path
**Learning:** Single-service exposure implies direct pivot opportunity; skip lateral recon entirely
 
**Decision →** Bypass SSL enforcement mismatch at client level
**Why:** Connection accepted by server but TLS handshake policy mismatch blocked authentication — server lacks proper SSL implementation despite requiring it
**Outcome:** Root-level database session established after disabling SSL at client
**Learning:** SSL requirement flag without server-side enforcement is a configuration mismatch, not a security boundary — always test SSL downgrade before assuming TLS blocks access
 
**Decision →** Deprioritize system schemas; pivot directly to application database
**Why:** System metadata schemas contain no exploitable data signals in single-service DB scenarios
**Outcome:** Application schema (`htb`) identified as exploitation boundary
**Learning:** Attack pivot occurs at application DB layer — system schemas are reconnaissance noise unless privilege escalation is already achieved
 
**Decision →** Inspect config tables before user tables
**Why:** Config tables in application schemas frequently store operational secrets, credentials, and flags as plaintext key-value pairs
**Outcome:** Flag retrieved directly from config table value field
**Learning:** Plaintext secrets in config tables are a primary DB exploitation pattern — config schema inspection should precede user table enumeration
 
---
 
## 3. High-Value Intelligence Layer
 
**Recon**
- Single database port exposure eliminates all lateral recon paths — no web pivot, no OS foothold required; attack surface is data-layer only from first contact
**Enumeration**
- System schemas produced no exploitable signals; application schema isolation (`htb`) defined clear exploitation boundary and confirmed custom data storage model
**Root Cause**
- Remote root login enabled on externally reachable MariaDB instance; SSL enforcement present as config flag but not implemented server-side; sensitive data stored as plaintext key-value pairs in application config table with no privilege separation between admin access and data layer
**Security Fix**
- Disable remote root database access entirely; enforce authenticated least-privilege DB users per application; migrate secrets from config tables into dedicated secret management (e.g., HashiCorp Vault or AWS Secrets Manager)
---
 
## 4. Pattern Recognition Engine
 
**Pattern: Exposed Database Single-Service Direct Pivot**
- **Signal:** Database port is the only externally reachable service — no auxiliary attack surface
- **Trigger:** Direct root or admin authentication succeeds without credential enumeration
- **Shortcut:** When DB is sole exposed service → assume direct data-level compromise path; skip OS and web exploitation entirely
- **Class:** MySQL/MariaDB (3306), PostgreSQL (5432), MSSQL (1433), MongoDB no-auth (27017), Redis (6379)
**Pattern: Config Table Secret Leakage**
- **Signal:** Application schema contains key-value structured table with operational parameter naming (`config`, `settings`, `options`, `app_config`)
- **Trigger:** Value field in config record contains flag, credential, or secret string
- **Shortcut:** Always enumerate config-style tables before user tables in any DB access scenario — operational secrets are stored at config layer, not user layer
---
 
## 5. Defensive Insight (Blue Team Mapping)
 
- **Fix:** Disable remote root login in `my.cnf` (`skip-networking` or explicit bind to `127.0.0.1`); enforce role-based DB users with minimum required privileges per application
- **Control:** Network segmentation blocking port 3306 from external access; secrets vault replacing all plaintext config table storage
- **Detection:** Alert on remote authentication events where username is `root` from non-localhost source IP; flag sessions where `SSL_CIPHER` is null or SSL handshake is absent on a server with `require_secure_transport` enabled
---
 
## 6. Mistakes & Corrections
 
- **Mistake:** Over-analysis of system schemas (`information_schema`, `performance_schema`) before identifying application database
- **Cause:** Treating metadata schemas as potential exploit surface rather than reconnaissance noise
- **Fix:** Identify and pivot to custom application databases first; enumerate system schemas only if privilege escalation context requires it
- **Future rule:** Custom DB first, system DB last — system schemas are noise until you already have escalated access