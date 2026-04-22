# 🛡️ Hack The Box (HTB) Labs

This repository contains structured write-ups, notes, and analysis for Hack The Box Starting Point and beginner-level machines.

The goal of this repo is not just to solve challenges, but to document the full security workflow:
**Recon → Enumeration → Exploitation → Impact Analysis**

---

## 🎯 Purpose

- Build hands-on cybersecurity skills (networking, Linux, exploitation basics)
- Practice structured penetration testing documentation
- Develop portfolio-ready security reports
- Track learning progress across machines

---

## 🧠 Methodology

Each machine is approached using a consistent workflow:

1. **Reconnaissance**
   - Network scanning (Nmap)
   - Service and version identification

2. **Enumeration**
   - Service-specific investigation (FTP, HTTP, SSH, etc.)
   - Access testing (e.g., anonymous login)

3. **Exploitation**
   - Misconfiguration or vulnerability exploitation
   - File or data extraction

4. **Post-Exploitation Analysis**
   - Identify root cause of vulnerability
   - Document security impact

---

# 🧱 HTB Labs Structure

```text
htb-labs/
│
├── README.md
│
├── Fawn_FTP_Anonymous_Access/
│   ├── README.md
│   ├── notes.md
│   ├── nmap.txt

```

Each folder represents a single machine with:
- Clean report (README.md)
- Raw notes (notes.md)
- Supporting artifacts (scan logs, sessions)

---

## 🧪 Machines Completed

| Machine | Category | Key Focus |
|----------|----------|-----------|
| Fawn | FTP | Anonymous access misconfiguration |

---

## 🔐 Key Learning Areas

- Network scanning and service discovery
- FTP protocol behavior and limitations
- Anonymous authentication risks
- File enumeration and retrieval techniques
- Basic vulnerability analysis and reporting

---

## ⚠️ Disclaimer

All content in this repository is based on legal, controlled environments provided by Hack The Box for educational purposes only.

No real-world systems are targeted.

---

## 🚀 Future Improvements Goal 

- Add more machines across different services (HTTP, SSH, SMB)
- Improve exploitation depth (beyond basic enumeration)
- Introduce privilege escalation analysis
- Develop structured attack decision trees

---

## 📌 Goal

To evolve from basic CTF problem-solving into structured security analysis and professional-level penetration testing documentation.
```

---

# 🧠 My Thinking

This is not:
- “I solved HTB boxes”

This is:
- **security learning pipeline documentation**
- shows methodology
- Record of my activity on HTB

