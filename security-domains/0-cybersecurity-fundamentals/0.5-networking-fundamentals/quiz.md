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

### Q10 — Final VLSM Challenge (Detailed Walkthrough & Answer)

**Pool:** `10.50.0.0/23` → covers `10.50.0.0 – 10.50.1.255` (512 total addresses)  
**Requirements:**  
- Dept A: **200 hosts**  
- Dept B: **60 hosts**  
- Dept C: **25 hosts**  
- Link D: **2 hosts** (point‑to‑point)

---

## 1) Plan with VLSM (Largest → Smallest)

Use the smallest subnet that provides **usable ≥ required**.  
Usable hosts = `2^(32−mask) − 2`

- **A (200 hosts)** → needs **/24** (256 total, **254 usable**) ✅  
  - /25 has 126 usable → too small ❌
- **B (60 hosts)** → needs **/26** (64 total, **62 usable**) ✅  
  - /27 has 30 usable → too small ❌
- **C (25 hosts)** → needs **/27** (32 total, **30 usable**) ✅  
- **D (2 hosts)** → needs **/30** (4 total, **2 usable**) ✅  
  - /31 has 2 total but 0 usable in classical accounting (point‑to‑point /31 is special case, but stick to /30 for exams)

**Chosen masks:** A=/24, B=/26, C=/27, D=/30

---

## 2) Carve the /23 Space (Binary‑Aligned, No Overlap)

The /23 spans two /24s back‑to‑back:
- Block 1: `10.50.0.0/24` → `10.50.0.0 – 10.50.0.255`
- Block 2: `10.50.1.0/24` → `10.50.1.0 – 10.50.1.255`

Allocate in order A → B → C → D:

### Dept A — /24
- **A:** `10.50.0.0/24`  
  - Network: `10.50.0.0`  
  - Broadcast: `10.50.0.255`  
  - Usable: `10.50.0.1 – 10.50.0.254` (254 hosts)

Remaining free space: the entire second /24 → `10.50.1.0/24`

### Dept B — /26 (64‑block aligned)
- A /26 increments by 64 in the last octet.
- Use start of the second /24 for clean alignment:
- **B:** `10.50.1.0/26`  
  - Network: `10.50.1.0`  
  - Broadcast: `10.50.1.63`  
  - Usable: `10.50.1.1 – 10.50.1.62` (62 hosts)

Remaining free in `10.50.1.0/24`: `10.50.1.64 – 10.50.1.255`

### Dept C — /27 (32‑block aligned)
- Next aligned /27 after 1.0/26 is **1.64/27** (64..95)
- **C:** `10.50.1.64/27`  
  - Network: `10.50.1.64`  
  - Broadcast: `10.50.1.95`  
  - Usable: `10.50.1.65 – 10.50.1.94` (30 hosts)

Remaining free: `10.50.1.96 – 10.50.1.255`

### Link D — /30 (4‑block aligned)
- Next aligned /30 after 1.64/27 (64..95) is **1.96/30** (96..99)
- **D:** `10.50.1.96/30`  
  - Network: `10.50.1.96`  
  - Broadcast: `10.50.1.99`  
  - Usable: `10.50.1.97 – 10.50.1.98` (2 hosts)

Remaining free (optional future use):  
- `10.50.1.100 – 10.50.1.255` (plenty of headroom for growth)

---

## 3) Final Allocation (Answer)

- **Dept A:** `10.50.0.0/24` → Usable `10.50.0.1 – 10.50.0.254` (254)  
- **Dept B:** `10.50.1.0/26` → Usable `10.50.1.1 – 10.50.1.62` (62)  
- **Dept C:** `10.50.1.64/27` → Usable `10.50.1.65 – 10.50.1.94` (30)  
- **Link D:** `10.50.1.96/30` → Usable `10.50.1.97 – 10.50.1.98` (2)

✅ Fits entirely within `10.50.0.0/23` with **no overlaps**, clean binary alignment.

---

## 4) Sanity Checks & Quick Tricks

- **Alignment:**  
  - /24 steps by 256 → `.0, .1, .2…` in the 3rd octet.  
  - /26 steps by 64 → `.0, .64, .128, .192` in the 4th octet.  
  - /27 steps by 32 → `.0, .32, .64, .96, …`  
  - /30 steps by 4 → `.0, .4, .8, .12, …`

- **Host math refresher:**  
  - /24 → 256 total, **254 usable**  
  - /26 → 64 total, **62 usable**  
  - /27 → 32 total, **30 usable**  
  - /30 → 4 total, **2 usable**

- **VLSM workflow:**  
  1) Sort subnets by **descending host need**.  
  2) Assign the **smallest mask** that meets each need.  
  3) Place each block at the **next aligned boundary**.  
  4) Ensure **no overlap** and within the parent prefix.

---

## 5) Interview One‑Liner
“From 10.50.0.0/23, allocate A=/24 (10.50.0.0/24), B=/26 (10.50.1.0/26), C=/27 (10.50.1.64/27), D=/30 (10.50.1.96/30). This order fits cleanly, aligns on proper boundaries, and leaves growth room.”

### Q4 — Real-World Curveball: Multi-Layer Attack Chain

**Scenario (Question):**  
During a red team engagement, you discover:  
1. An access switch has no port security. You launch a **MAC flooding attack**, overflowing the CAM table.  
2. You then perform **ARP spoofing** to impersonate the default gateway and capture traffic.  
3. Finally, you manipulate a router’s **routing table** using **ICMP redirect messages**, forcing packets through your rogue system.  

**Question:**  
1. Map each step to the correct OSI/TCP-IP layer.  
2. Explain how these weaknesses chain together.  
3. Suggest one defense measure for each step.  

---

**Answer:**

**Layer Mapping**  
- MAC Flooding → Data Link (L2) / Link Layer  
- ARP Spoofing → Data Link (L2) / Link Layer  
- ICMP Redirect → Network (L3) / Internet Layer  

---

**Attack Chain (Red Team View)**  
- **MAC Flooding:** Overflows CAM → switch acts like hub → attacker captures frames.  
- **ARP Spoofing:** Attacker impersonates gateway → becomes MITM → sniffs credentials, injects payloads.  
- **ICMP Redirect:** Attacker manipulates routing → hosts forward traffic via attacker → full packet control.  

---

**Blue Team Defenses**  
- **MAC Flooding:** Enable port security (limit MACs per port), disable unused ports.  
- **ARP Spoofing:** Deploy Dynamic ARP Inspection (DAI), static ARP for gateways, enforce TLS/SSH.  
- **ICMP Redirect:** Disable ICMP redirects on routers, authenticate routing protocols, monitor routing changes.  

---

**Interview One-Liner**  
“MAC flooding (L2) forces hub-like behavior, ARP spoofing (L2) enables MITM, and ICMP redirects (L3) manipulate routing. Defenses: port security, ARP inspection, and disabling ICMP redirects.”

### Q4 — Real-World Curveball: Defense in Depth

**Scenario (Question):**  
During a red team exercise:  
1. You try brute-forcing SSH on `10.0.0.12:22`, but the **firewall blocks all inbound SSH except from a VPN subnet**.  
2. You pivot and launch a **SQL injection** against the company’s web app. The **IDS (Snort)** raises an alert in the SOC dashboard, but traffic still passes through.  
3. You then upload a malicious file through the web app. The **IPS (inline, Suricata)** detects it as a known malware signature and **drops the upload in real time**.  

**Question:**  
1. Which security control (Firewall, IDS, IPS) was effective in each step?  
2. How do these three layers complement each other in defense-in-depth?  
3. What’s one limitation of relying only on one of them?  

---

**Answer:**

1. **Controls Effective at Each Step**  
   - Step 1 → Firewall blocked brute-force SSH.  
   - Step 2 → IDS detected SQL injection (alert only).  
   - Step 3 → IPS detected malware and blocked it in real time.  

2. **Defense-in-Depth**  
   - Firewall enforces network access control.  
   - IDS provides visibility and alerts SOC of suspicious traffic.  
   - IPS actively blocks known exploits and malware inline.  
   - Together: layered security = prevent, detect, and respond.  

3. **Limitations of Relying on One**  
   - Firewall only: Can’t see application-layer attacks (SQLi, XSS).  
   - IDS only: Detects but doesn’t block → attacker may still succeed.  
   - IPS only: Can block, but may cause false positives and lacks broader visibility.  

---

**Interview One-Liner**  
“In this chain, Firewall blocked brute-force, IDS detected SQLi, IPS blocked malware. Each alone has limits, but together they form effective defense-in-depth.”

### Q1 — Comprehensive Scenario: Wireshark & tcpdump

**Scenario (Question):**  
You are a SOC analyst investigating suspicious outbound traffic from a Linux server.  
1. Using **tcpdump**, capture only traffic from the server to suspicious IP `203.0.113.50` on port `443` and save it for later analysis.  
2. Load this capture into **Wireshark**. What filters would you apply to:  
   - View only HTTP(S) requests?  
   - Follow the entire TCP session between client and server?  
   - Check for unusual DNS tunneling activity?  
3. As part of **incident response**, what are the main differences in using tcpdump vs Wireshark in this scenario?  
4. What are **two best practices** when performing packet capture in production networks?  

---

**Answer:**

1. **tcpdump Command:**  
   tcpdump -i eth0 host 203.0.113.50 and port 443 -w suspicious.pcap  

2. **Wireshark Filters:**  
   - Only HTTP(S): `http` or `tcp.port==443`  
   - Follow full session: Right-click packet → **Follow → TCP Stream**  
   - DNS tunneling: `dns` (look for TXT records, long queries, repetitive traffic patterns)  

3. **tcpdump vs Wireshark:**  
   - tcpdump = lightweight CLI capture, great for remote servers and quick forensics.  
   - Wireshark = GUI deep analysis, powerful filtering, protocol hierarchy, reassembly, visualization.  
   - tcpdump captures the data → Wireshark dissects it.  

4. **Best Practices:**  
   - Always use filters to avoid massive `.pcap` files.  
   - Run as root/admin but restrict capture scope (least privilege).  
   - Securely transfer `.pcap` files for central analysis (don’t leave on compromised host).  
   - Respect privacy/legal constraints when capturing user traffic.  

---

**Interview One-Liner**  
“tcpdump captures raw packets (CLI, quick, filterable), Wireshark analyzes in detail (GUI, full OSI inspection). Best practice: filter captures, minimize scope, and centralize for correlation.”