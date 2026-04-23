# Machine: Redeemer
 
- **Environment:** HTB Starting Point
- **Difficulty:** Very Easy
- **Tags:** Redis, Exposed Service, Unauthenticated Datastore, Linux
---
 
## 1. Executive Summary
 
- **Vulnerability:** Unauthenticated Redis instance exposed on network
- **Entry vector:** Direct network access to in-memory key-value store on non-standard port
- **Exploit result:** Full read access to database keys including sensitive flag
- **Root cause:** Redis deployed without `requirepass`, bound to all interfaces, no firewall restriction
- **Real-world equivalent:** Publicly reachable internal datastore via cloud security group misconfiguration or dev environment leak
---
 
## 2. Attack Path (Decision Chain ONLY)
 
**Decision →** Expand port scope beyond default scan range
**Why:** No services visible in standard top-1000 port range — service had to exist outside default visibility
**Outcome:** Redis identified on non-standard port outside baseline enumeration scope
**Learning:** Service visibility gap exists until full port space is validated — always run full range on empty results
 
**Decision →** Interact directly with discovered database service
**Why:** Redis class indicates in-memory key-value store with historically weak default trust model
**Outcome:** Unauthenticated session established immediately — no credential challenge
**Learning:** Redis exposure = high trust assumption failure; authentication is opt-in, not default
 
**Decision →** Enumerate logical data objects inside database
**Why:** Key-value stores have no inherent schema — attack surface is inferred via key enumeration
**Outcome:** Full key space revealed; sensitive object identified
**Learning:** Flat datastore enumeration replaces traditional filesystem traversal — keys are the directory
 
**Decision →** Extract value from sensitive key
**Why:** No access control, RBAC, or privilege separation present
**Outcome:** Flag retrieved in plaintext from memory
**Learning:** Missing auth layer = direct memory-level read; data exposure is instantaneous once session is open
 
---
 
## 3. High-Value Intelligence Layer
 
**Recon**
- Non-web infrastructure exposed externally; in-memory datastore presence signals high-risk misconfiguration class — attack surface shifts from OS exploitation to data-layer direct access
**Enumeration**
- Key discovery confirms absence of segmentation or RBAC; all stored objects globally readable — exploit direction collapses from probing to direct extraction
**Root Cause**
- Redis deployed without `requirepass`, bound to `0.0.0.0` instead of localhost, and no firewall rule blocking port 6379 externally — three independent control failures each sufficient to cause full exposure independently
**Security Fix**
- Set `requirepass` + `bind 127.0.0.1` in `redis.conf`; block port 6379 at network perimeter; add TLS if Redis must communicate across network boundaries
---
 
## 4. Pattern Recognition Engine
 
**Pattern: Exposed In-Memory Datastore**
- **Signal:** Port associated with key-value or in-memory store reachable externally
- **Trigger:** Unauthenticated session established + key enumeration succeeds immediately
- **Shortcut:** If datastore port is externally reachable → assume full read/write access until proven otherwise; authentication is never guaranteed by default
- **Class:** Redis (6379), Memcached (11211), MongoDB no-auth (27017), Elasticsearch (9200), etcd (2379)
**Pattern: Flat Namespace Data Leakage**
- **Signal:** Ability to enumerate arbitrary keys without schema constraints or permission gates
- **Trigger:** Global key listing returns sensitive object names (e.g., `flag`, `config`, `session`, `token`)
- **Shortcut:** Key-value stores without auth behave as unrestricted memory dumps — treat any readable key as immediate exfiltration target
---
 
## 5. Defensive Insight (Blue Team Mapping)
 
- **Fix:** Enable `requirepass` in `redis.conf`; set `bind 127.0.0.1` to prevent external interface exposure
- **Control:** Firewall deny-rule on port 6379 for all external sources; place Redis behind internal service boundary only
- **Detection:** Alert on Redis `KEYS *`, `CONFIG GET`, or `DEBUG` commands originating from external IPs; flag any session where `AUTH` command is absent but data commands (`GET`, `SET`, `KEYS`) execute successfully
---
 
## 6. Mistakes & Corrections
 
- **Mistake:** Assumed no services present after default port scan returned empty results
- **Cause:** Default scan covers top 1000 ports only — Redis on 6379 falls outside this range
- **Fix:** Run full TCP range scan (`-p-`) whenever default results are empty or unexpectedly sparse
- **Future rule:** Empty default scan = service visibility gap, not absence of services; full range is mandatory before concluding attack surface is limited
 