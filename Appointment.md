# Machine: Appointment
 
- **Environment:** HTB Starting Point
- **Difficulty:** Very Easy
- **Tags:** Web, SQL Injection, Authentication Bypass, Apache, Linux
---
 
## 1. Executive Summary
 
- **Vulnerability:** SQL injection in authentication logic via unsanitized input
- **Entry vector:** Login form accepting raw user input concatenated directly into SQL query
- **Exploit result:** Authentication bypassed as admin via comment-based query truncation
- **Root cause:** Backend query built with string concatenation — no parameterization, no input validation
- **Real-world equivalent:** Legacy web login systems with hand-built SQL queries still common in internal enterprise portals
---
 
## 2. Attack Path (Decision Chain ONLY)
 
**Decision →** Identify exposed service and reduce attack surface
**Why:** Single HTTP service means application logic is the only viable vector — no network enumeration required
**Outcome:** Login interface confirmed as primary target
**Learning:** Single-service exposure increases probability of logic-layer flaws — go straight to application behavior
 
**Decision →** Test authentication boundary with generic input to observe error behavior
**Why:** Determine whether backend validates credentials deterministically or exposes structural feedback
**Outcome:** No meaningful error differentiation — backend behavior is opaque
**Learning:** Uniform error responses on login forms often mask SQL-backed authentication logic
 
**Decision →** Apply SQL comment injection payload instead of credential guessing
**Why:** Comment-based truncation removes password clause from query execution path entirely
**Outcome:** Password validation stripped from query — authentication logic bypassed structurally
**Learning:** Query truncation is deterministic; brute force is probabilistic — structural attacks always win when injection is present
 
**Decision →** Validate post-injection session state
**Why:** Confirm injection produced privilege-level access, not just error suppression
**Outcome:** Admin session achieved — flag exposed
**Learning:** Logic bypass vulnerabilities manifest as full auth state transitions, not partial access
 
---
 
## 3. High-Value Intelligence Layer
 
**Recon**
- Single HTTP service collapsed attack surface to application layer only; no network enumeration required — focus shifts immediately to login logic
**Enumeration**
- Login form behavior consistent with direct SQL query construction; absence of ORM-style error handling confirmed raw concatenation pattern
**Root Cause**
- Authentication query built via string concatenation of raw user input — comment injection truncates the password clause, making password verification structurally unreachable at query level
**Security Fix**
- Replace all authentication queries with parameterized prepared statements; enforce server-side input validation with explicit type and character constraints on credential fields
---
 
## 4. Pattern Recognition Engine
 
**Pattern: Authentication Query Concatenation Failure**
- **Signal:** Login form accepts arbitrary characters without rejection or sanitization feedback
- **Trigger:** Authentication succeeds using SQL comment token (`#` or `--`) with no valid password
- **Shortcut:** If login input maps directly to SQL structure → assume injection class; test comment truncation before any other payload
- **Class:** Login forms on PHP/MySQL stacks, legacy ASP/MSSQL portals, any hand-built auth query without ORM layer
**Pattern: Comment-Based Query Truncation**
- **Signal:** Special characters (`#`, `--`, `/* */`) accepted in credential fields without rejection
- **Trigger:** Successful authentication with structurally invalid or absent password value
- **Shortcut:** If password becomes irrelevant to login outcome → backend query is being structurally altered, not just evaluated incorrectly
---
 
## 5. Defensive Insight (Blue Team Mapping)
 
- **Fix:** Convert all authentication queries to parameterized prepared statements — string concatenation must be eliminated at code level
- **Control:** WAF rule blocking SQL comment tokens (`#`, `--`, `/*`) in authentication field inputs; server-side allowlist validation on credential format
- **Detection:** Alert on authentication success events where password field contains SQL metacharacters (`#`, `--`, `'`, `/*`) or where login succeeds after a prior failed attempt with the same username within the same session
---
 
## 6. Mistakes & Corrections
 
- **Mistake:** Initial POST requests sent without analyzing server-side response state transitions
- **Cause:** Focus on payload success rather than behavioral change in HTTP response headers and status codes
- **Fix:** Inspect HTTP status codes, redirect chains, and session cookie changes alongside response body
- **Future rule:** Authentication testing must validate state transition (session established, privilege level changed) — response body alone is insufficient confirmation
---
 
## 7. Decision Layer
 
**Option A:** Credential guessing (brute force / default credentials)
**Option B:** SQL injection authentication bypass
 
**Why B wins:**
- Eliminates password space entirely — no enumeration required
- Exploits structural flaw rather than probabilistic guessing
- Produces immediate full privilege escalation in one request
**Tradeoff insight:**
- Option A generates high request volume — detectable via login failure rate anomalies
- Option B is a single deterministic request — silent if WAF or logging is absent; devastating if parameterization is missing
 