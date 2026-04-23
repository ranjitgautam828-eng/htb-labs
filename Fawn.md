# Machine: Fawn

* Environment: HTB (Starting Point)
* Difficulty: Very Easy
* Tags: FTP, Anonymous Access, Misconfiguration, File Exposure

---

## 1. Executive Summary

* FTP service exposed with anonymous login enabled
* Access achieved without credentials via default configuration
* Sensitive file (`flag.txt`) publicly accessible
* Root cause: misconfigured FTP allowing unauthenticated access
* Real-world impact: direct data exposure due to weak service configuration

---

## 2. Attack Path (Reasoned Flow)

### Step 1: Service Identification

* Action: Network scan performed (Nmap)
* Why: Identify open ports and services
* Result: FTP detected on port 21 (vsftpd 3.0.3)
* Insight: File transfer service exposed externally

---

### Step 2: Service Interaction

* Action: Connected to FTP service
* Why: Validate service behavior beyond scan results
* Result: FTP login prompt received
* Insight: Service is actively accepting connections

---

### Step 3: Authentication Testing

* Action: Attempted anonymous login
* Why: Check default FTP misconfiguration
* Result: Login successful without credentials
* Insight: Anonymous access enabled (critical misconfiguration)

---

### Step 4: Enumeration

* Action: Listed directory contents (`ls`)
* Why: Identify accessible files
* Result: `flag.txt` discovered
* Insight: Sensitive file exposed in public directory

---

### Step 5: Data Extraction

* Action: Downloaded file using FTP transfer command (`get`)
* Why: Retrieve exposed file
* Result: File successfully downloaded locally
* Insight: No access control or restrictions in place

---

## 3. Root Cause Analysis

* Anonymous FTP login enabled
* No authentication enforcement
* Sensitive file stored in publicly accessible directory
* Poor directory access control
* Misconfiguration rather than exploit-based vulnerability

---

## 4. Pattern Recognition

### Vulnerability Pattern: Anonymous Service Exposure

* Common in FTP, SMB, legacy storage services

### Indicators:

* Login possible without credentials
* "anonymous" authentication accepted
* Immediate file listing after login
* No authentication barrier before access

### Recognition Rule:

If FTP (port 21) is open → always test anonymous login first

---

## 5. Defensive Insight

### Fixes:

* Disable anonymous FTP access
* Enforce authentication (credentials or key-based access)
* Restrict directory permissions (least privilege)
* Separate public vs private file storage

### Monitoring:

* Log anonymous FTP attempts
* Alert on unauthorized file downloads
* Monitor bulk file transfer activity

---

## 6. Mistakes & Corrections

* Attempted `cat` inside FTP client

  * Issue: FTP is not a shell environment
  * Fix: Use `get` to download files

* Initial assumption that authentication would be required

  * Correction: Validated via anonymous login test

---

## Final Outcome

FTP misconfiguration allowed unauthenticated access, resulting in successful retrieval of exposed sensitive file. This highlights the importance of secure service configuration and access control enforcement.
