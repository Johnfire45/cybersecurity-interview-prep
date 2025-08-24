###  Final Curveball: Defense Strategy Mapping

**Scenario (Question):**  
As a Blue Team defender, you propose these security controls:  
1. RF shielding & locked server racks  
2. Dynamic ARP inspection (DAI) on switches  
3. Ingress/Egress IP ACLs & anti-spoof filters on routers  
4. SYN cookies & rate-limiting at firewalls  
5. TLS 1.3 enforcement for all web apps  
6. WAF deployed in front of e-commerce apps  

**Question:**  
Map each control to the correct OSI layer (L1–L7).  

---

**Answer:**

- **Layer 1 (Physical):** RF shielding & locked racks → protects physical medium from tampering or interference.  
- **Layer 2 (Data Link):** Dynamic ARP inspection → prevents ARP spoofing/MAC-based attacks.  
- **Layer 3 (Network):** IP ACLs & anti-spoof filters → block malicious or spoofed IP packets.  
- **Layer 4 (Transport):** SYN cookies & rate-limiting → mitigate TCP SYN floods and DoS at transport level.  
- **Layer 6 (Presentation):** TLS 1.3 enforcement → encryption/translation to secure application data.  
- **Layer 7 (Application):** WAF in front of apps → filters HTTP/HTTPS traffic, blocks SQLi, XSS, etc.  

---

**Interview One-Liner**  
“Physical hardening maps to L1, ARP inspection to L2, ACLs to L3, SYN cookies to L4, TLS 1.3 to L6, and WAF protections to L7.”  

### Q — Real-World Curveball: Multi-Layer Attack on TCP/IP

**Scenario (Question):**  
During a red team engagement, you capture traffic showing:  
- Large numbers of spoofed ICMP Echo Requests with random source IPs flooding a target.  
- Half-open TCP connections to port 443 with no ACKs completing the handshake.  
- Users redirected to a fake DNS server controlled by the attacker.  

**Question:**  
1. Identify which TCP/IP layer each attack occurs at.  
2. Explain how encapsulation/decapsulation helps pinpoint which header fields are abused.  
3. As a defender, propose one detection method and one mitigation strategy for each attack.  

---

**Answer:**

**Layer Mapping**  
- ICMP Flood → **Internet Layer (L3)**  
- TCP SYN Flood → **Transport Layer (L4)**  
- DNS Poisoning → **Application Layer (L7)**  

---

**Encapsulation Abuse**  
- ICMP flood → abuses **IP header (spoofed source IPs)** + **ICMP fields (type/code)**.  
- SYN flood → abuses **TCP header flags (SYN)** leaving half-open connections.  
- DNS poisoning → abuses **Application headers (fake DNS responses)**.  

---

**Defense Strategies**  
- **ICMP Flood (L3):**  
  - Detection: NIDS/NIPS flagging abnormal ICMP rates.  
  - Mitigation: ICMP rate limiting, ingress/egress filtering.  

- **SYN Flood (L4):**  
  - Detection: Monitor for excessive half-open sessions (SYN_RECV).  
  - Mitigation: Enable SYN cookies, firewall throttling.  

- **DNS Poisoning (L7):**  
  - Detection: Compare resolver answers with trusted sources, detect mismatches.  
  - Mitigation: Deploy DNSSEC, restrict recursive DNS, monitor resolver configs.  

---

**Interview One-Liner**  
“In TCP/IP, ICMP floods hit Internet layer, SYN floods hit Transport, DNS poisoning hits Application. Each layer’s headers (IP, TCP, DNS) show what’s abused. Defenses: ICMP filtering, SYN cookies, and DNSSEC.”

### Q2 — Suspicious Fake Daemon

**Scenario (Question):**  
A Linux server shows two `apache2` processes.  
- One is legitimate (Apache web server on port 8080).  
- Another suspicious one listens on port 4444.  

**Question:**  
1. From a Red Team perspective, why would an attacker set up a fake daemon like this?  
2. From a Blue Team perspective, how would you detect and respond?  

---

**Answer:**

**Red Team Perspective**  
- Attacker disguises a backdoor as a common service (`apache2`).  
- Runs on non-standard port (4444) to act as a **reverse shell/backdoor listener**.  
- Goal: persistence + stealth, since admins may ignore duplicate `apache2` processes.  

---

**Blue Team Perspective**  
- **Detection:**  
  - Use `ss -tulnp` / `netstat` to see processes bound to ports.  
  - Compare daemons against baseline (only one Apache should exist).  
  - Verify binary paths (`which apache2` vs. `/proc/<pid>/exe`).  
  - Use IDS/HIDS (OSSEC, Wazuh) to flag rogue daemons.  

- **Response:**  
  - Preserve forensic evidence, then kill the rogue process.  
  - Isolate host to cut off attacker’s C2.  
  - Investigate logs for entry vector.  
  - Rebuild/restore if root compromise confirmed.  

---

**Interview One-Liner**  
“Attackers spawn fake daemons (e.g., `apache2` on port 4444) to hide backdoors. Defenders catch them with port/process monitoring and integrity checks, then isolate and eradicate.”  

### Q4 — Final Curveball: Full Attack Surface Scenario

**Scenario (Question):**  
During a red team assessment of a production server, you find:  
1. Port 21 (FTP) is open with anonymous login enabled.  
2. MySQL daemon (`mysqld`) is running on port 3306, accessible from the internet.  
3. A rogue daemon named `cron-update` is running on port 9001 and set to auto-restart via systemd.  
4. SMB/NetBIOS ports 135–139 and 445 are open but unused.  

**Question:**  
1. From a Red Team perspective, how could each of these findings be abused in an attack chain?  
2. From a Blue Team perspective, what immediate hardening actions should be applied?  

---

**Answer:**

**Red Team Perspective**  
- **FTP (21, anonymous login):** Upload malicious files, download sensitive data, pivot into system.  
- **MySQL (3306, public):** Default creds/brute-force → DB dump, modification, or RCE exploit.  
- **Rogue Daemon (9001):** Backdoor persistence via systemd; attacker regains access every reboot.  
- **SMB/NetBIOS (135–139, 445):** Exploits (e.g., EternalBlue), lateral movement, credential theft, share enumeration.  

---

**Blue Team Perspective**  
- **FTP:** Disable anonymous login or migrate to SFTP; enforce auth controls.  
- **MySQL:** Restrict to localhost/private subnet; enforce strong creds; block public access.  
- **Rogue Daemon:** Identify via `ss/netstat`; disable and remove malicious systemd unit; investigate compromise.  
- **SMB/NetBIOS:** Close unused ports via firewall (iptables, ufw, firewalld).  
- **General:** Baseline allowed services, patch regularly, monitor with IDS/IPS, apply least privilege.  

---

**Interview One-Liner**  
“Attackers would chain anonymous FTP, exposed MySQL, rogue daemons, and open SMB ports for persistence and lateral movement. Defenders must shut down unused services, restrict DB access, remove rogue daemons, and enforce least privilege with firewall + monitoring.”  

### Q2 — Protocol Security Scenario

**Scenario (Question):**  
During a red team assessment, you observe:  
1. A web app on port 80 allows login over plain HTTP.  
2. An NTP server on port 123/UDP is responding to anyone on the internet.  
3. An SMTP server on port 25 is accepting mail without authentication.  

**Question:**  
For each case:  
1. Identify which OSI/TCP-IP layer the weakness belongs to.  
2. Describe the attack risk it introduces.  

---

**Answer:**

1. **HTTP on port 80 (no TLS)**  
   - **Layer:** Application (L7).  
   - **Risk:** Credentials in cleartext → Man-in-the-Middle (MITM) sniffing.  
   - **Defense:** Enforce HTTPS (TLS 1.2/1.3).  

2. **NTP on port 123/UDP (open access)**  
   - **Layer:** Transport (UDP) + Application (NTP).  
   - **Risk:** Abuse in amplification DDoS attacks.  
   - **Defense:** Restrict NTP queries, apply rate limiting.  

3. **SMTP on port 25 (no authentication)**  
   - **Layer:** Application (L7).  
   - **Risk:** Open relay → spoofed spam/phishing emails, reputation damage.  
   - **Defense:** Require auth, enforce SPF/DKIM/DMARC.  

---

**Interview One-Liner**  
“HTTP on 80 without TLS = MITM, NTP open = amplification DDoS, SMTP unauth = open relay/spoofing. Fixes: TLS, restrict NTP, enforce SPF/DKIM/DMARC.”  

# 0.5.5 Network Addressing — Quiz Summary

---

### Q1 — Subnetting Basics
**Question:** For the subnet 192.168.10.0/24:  
- How many usable hosts?  
- What are the network and broadcast addresses?  

**Answer:**  
- Usable hosts: **254**  
- Network: **192.168.10.0**  
- Broadcast: **192.168.10.255**

---

### Q2 — Subnetting Twist
**Question:** For subnet 10.0.5.128/26, which values are correct?  

**Answer:**  
- Network: **10.0.5.128**  
- Broadcast: **10.0.5.191**  
- Usable hosts: **62**  
- First usable: 10.0.5.129  
- Last usable: 10.0.5.190

---

### Q3 — CIDR/Subnetting
**Question:** Host 172.16.8.200/27 belongs to which subnet?  

**Answer:**  
- Network: **172.16.8.192**  
- Broadcast: **172.16.8.223**  
- Usable hosts: **172.16.8.193–172.16.8.222**  
- Usable count: 30

---

### Key Interview Takeaways
- A **/24** has 254 usable hosts.  
- A **/26** subnet splits into blocks of 64, each with 62 usable hosts.  
- A **/27** has a block size of 32 → 30 usable hosts.  
- **Quick trick:** Block size = 2^(32 − subnet mask). Network = nearest multiple of block size, Broadcast = Network + Block size − 1.