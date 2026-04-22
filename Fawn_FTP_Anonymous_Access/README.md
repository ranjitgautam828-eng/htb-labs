# HTB Starting Point - Fawn

Fawn is a beginner-level HTB machine focused on FTP misconfiguration. It demonstrates anonymous access exploitation, basic enumeration, and file retrieval from an exposed FTP service.

## 1. Target Info
- Machine: Fawn
- OS: Unix (Linux-based)
- Service: FTP (vsftpd 3.0.3)
- Port: 21

---

## 2. Recon Phase
- Tool used: nmap
- Open service detected: FTP (21/tcp)
- Version: vsftpd 3.0.3

---

## 3. Access Phase
- Method: FTP login
- Username used: anonymous
- Result: Login successful (230)

---

## 4. Enumeration Phase
- Directory listing performed using `ls`
- Found file: flag.txt

---

## 5. Exploitation Phase
- Downloaded file using: `get flag.txt`
- File transferred successfully

---

## 6. Result
- Flag retrieved successfully from FTP server

---

## 7. Vulnerability Analysis
- Anonymous FTP login enabled
- No authentication required for file access
- Sensitive file exposed in public directory

---

## 8. Key Lessons
- FTP is not a shell (cannot use `cat`)
- Must use `get` to retrieve files
- Always test anonymous login in FTP services

##  Sensitive file successfully extracted via anonymous FTP access
