# 0.5.1 OSI Model Layers (L1–L7)

## 1. Overview
- OSI = **Open Systems Interconnection Model**, conceptual framework with 7 layers.  
- Standardizes how data moves through networks from physical transmission → user application.  

---

## 2. Layers and Security Relevance

### Layer 1 — Physical
- Raw bit transmission over media (cables, fiber, radio).  
- Devices: hubs, repeaters.  
- **Attacks:** wiretapping, jamming, cable tampering.  
- **Defenses:** physical security, shielding, port locks.  

### Layer 2 — Data Link
- Responsible for framing, MAC addressing, error detection.  
- Protocols: Ethernet, Wi-Fi, ARP.  
- **Attacks:** MAC spoofing, ARP spoofing, VLAN hopping.  
- **Defenses:** switch port security, ARP inspection, DHCP snooping.  

### Layer 3 — Network
- Logical addressing and routing (IP, ICMP, OSPF, BGP).  
- **Attacks:** IP spoofing, route hijacking, ICMP floods.  
- **Defenses:** ACLs, route validation, anti-spoof filters, firewalls.  

### Layer 4 — Transport
- End-to-end communication, segmentation, reliability.  
- Protocols: TCP (reliable), UDP (fast, connectionless).  
- **Attacks:** TCP SYN floods, UDP floods, session hijacking, RST injection.  
- **Defenses:** rate limiting, SYN cookies, IDS/IPS.  

### Layer 5 — Session
- Establishes, manages, terminates sessions.  
- Examples: NetBIOS, RPC, TLS session mgmt.  
- **Attacks:** session hijacking, replay attacks.  
- **Defenses:** secure tokens, TLS session resumption, timeouts.  

### Layer 6 — Presentation
- Data translation, compression, encryption.  
- Examples: SSL/TLS, ASCII, JPEG.  
- **Attacks:** weak crypto (SSLv2/3), downgrade attacks.  
- **Defenses:** enforce TLS 1.2/1.3, disable weak ciphers/protocols.  

### Layer 7 — Application
- Closest to user, provides application services.  
- Examples: HTTP, HTTPS, DNS, FTP, SMTP.  
- **Attacks:** SQL injection, XSS, CSRF, DNS tunneling.  
- **Defenses:** secure coding, input validation, WAF, protocol monitoring.  

---

## 3. Interview One-Liners
- “OSI Model = 7 layers from physical (L1) to application (L7).”  
- “Each layer adds headers and services: bits → frames → packets → segments → sessions → encryption → apps.”  
- “Security mapping:  
  - L1 physical tampering  
  - L2 spoofing (ARP/MAC)  
  - L3 IP spoofing/route hijacking  
  - L4 floods, hijacking  
  - L5 session hijacking  
  - L6 weak crypto  
  - L7 app-layer attacks.”  

  # 0.5.2 TCP/IP Stack & Packets

## 1. TCP/IP vs OSI
- **OSI Model:** 7 conceptual layers (L1–L7).
- **TCP/IP Model:** 4 practical layers:
  1. Link Layer (≈ OSI L1–L2)
  2. Internet Layer (≈ OSI L3)
  3. Transport Layer (≈ OSI L4)
  4. Application Layer (≈ OSI L5–L7)

---

## 2. TCP/IP Layers and Security Relevance

### Link Layer (Network Interface Layer)
- Moves frames over physical medium.
- Protocols: Ethernet, Wi-Fi, ARP, PPP.
- **Attacks:** ARP spoofing, MAC flooding, VLAN hopping.
- **Defenses:** ARP inspection, MAC filtering, switch security features.

### Internet Layer
- Logical addressing & routing between networks.
- Protocols: IPv4, IPv6, ICMP, IGMP.
- **Attacks:** IP spoofing, ICMP floods, route hijacking.
- **Defenses:** ACLs, ingress/egress filtering, anti-spoof checks.

### Transport Layer
- Provides reliable (TCP) or fast (UDP) communication.
- **TCP:** connection-oriented (3-way handshake, retransmission).
- **UDP:** connectionless, faster, lightweight.
- **Attacks:** TCP SYN floods, UDP floods, session hijacking.
- **Defenses:** SYN cookies, rate limiting, IDS/IPS.

### Application Layer
- Provides services to user-facing apps.
- Protocols: HTTP/HTTPS, DNS, FTP, SMTP, SNMP, SSH.
- **Attacks:** SQL injection, XSS, CSRF, DNS spoofing/tunneling, protocol abuse.
- **Defenses:** WAF, secure coding, DNSSEC, protocol monitoring.

---

## 3. Encapsulation & Decapsulation
- Sending: Data flows down the stack → headers added.
  - Application header (e.g., HTTP)
  - Transport header (TCP/UDP ports)
  - Internet header (IP addresses)
  - Link header (MAC addresses, framing)
- Receiving: reverse process (strip headers).

---

## 4. Packet Structures
- **Ethernet Frame (L2):** Dest MAC | Src MAC | EtherType | Payload | FCS
- **IP Packet (L3):** IP Header (Src IP, Dest IP, TTL, Protocol) | Data
- **TCP Segment (L4):** Src Port | Dest Port | Seq# | Ack# | Flags | Data

---

## 5. Interview One-Liners
- “OSI is 7 layers, TCP/IP is 4 practical layers.”
- “Encapsulation adds headers at each layer, reversed during decapsulation.”
- **Security mapping:**
  - Link → ARP/MAC spoofing
  - Internet → IP spoofing, ICMP abuse
  - Transport → SYN floods, hijacking
  - Application → SQLi, XSS, protocol abuse

  Here’s the TCP 3-Way Handshake with packet breakdown:
	1.	SYN → Client → Server
	•	Src Port: 12345 | Dst Port: 80 | Seq=1000 | Flags=SYN
	2.	SYN-ACK → Server → Client
	•	Src Port: 80 | Dst Port: 12345 | Seq=5000 | Ack=1001 | Flags=SYN,ACK
	3.	ACK → Client → Server
	•	Src Port: 12345 | Dst Port: 80 | Seq=1001 | Ack=5001 | Flags=ACK

This shows exactly how ports, sequence numbers, and flags evolve to establish a reliable TCP session.

# 0.5.3 Ports, Services & Daemons

## 1. Ports
- Logical endpoints for communication (IP + protocol).
- **Range:** 0–65535
  - **Well-known (0–1023):** Reserved (22 SSH, 80 HTTP, 443 HTTPS).
  - **Registered (1024–49151):** Vendor apps (3306 MySQL, 8080 HTTP-alt).
  - **Dynamic/Ephemeral (49152–65535):** Temporary client-side connections.
- **Security Risks:** Port scanning, open/unused ports, default configs.

---

## 2. Services
- Software providing functionality over a port/protocol.
- Examples: SSH (22), HTTP/HTTPS (80/443), DNS (53), SMTP (25).
- Services can run on non-standard ports (security by obscurity).
- **Security Risks:** Misconfigured services, weak credentials, exposed DBs, banner grabbing for version fingerprinting.

---

## 3. Daemons
- Background processes that power services.
- Examples: `sshd`, `httpd`, `mysqld`, `systemd-resolved`.
- Started by init system at boot (systemd, SysVinit).
- **Security Risks:** Compromised daemons act as persistent backdoors, often disguised as legitimate processes; root-level daemons amplify damage.

---

## 4. Attack Vectors
- **Port Scanning:** Reconnaissance with tools (`nmap`, `masscan`).
- **Service Exploits:** Exploiting vulnerable versions (e.g., Heartbleed, Apache Struts RCE).
- **Backdoor Daemons:** Hidden malicious daemons or trojanized systemd units.

---

## 5. Defense Strategies
- Close unused ports (`iptables`, `ufw`, `firewalld`).
- Monitor with `netstat -tulnp`, `ss -tulnp`, `lsof -i`.
- Principle of least privilege: run services as unprivileged users, sandbox when possible.
- Patch services and daemons regularly.
- Use host firewalls + IDS/IPS.
- Monitor process integrity to detect rogue daemons.

---

## 6. Interview One-Liners
- “Ports are logical endpoints, services expose functionality, and daemons are background processes running services.”
- “Attackers exploit open ports and vulnerable daemons; defenders minimize attack surface and monitor continuously.”
- “Least privilege, patching, and firewalls are key to hardening.”

# Common Protocols by Ascending Port Number

- **FTP (File Transfer Protocol)** → 20 (data), 21 (control)  
- **SSH (Secure Shell)** → 22  
- **SMTP (Simple Mail Transfer Protocol)** → 25  
- **DNS (Domain Name System)** → 53 (UDP/TCP)  
- **DHCP (Dynamic Host Configuration Protocol)** → 67/68 (UDP) *(not detailed above, but commonly expected)*  
- **TFTP (Trivial File Transfer Protocol)** → 69 (UDP) *(optional, legacy)*  
- **HTTP** → 80  
- **POP3 (Post Office Protocol v3)** → 110  
- **NTP (Network Time Protocol)** → 123 (UDP)  
- **IMAP (Internet Message Access Protocol)** → 143  
- **SNMP (Simple Network Management Protocol)** → 161 (queries), 162 (traps)  
- **HTTPS** → 443  
- **SMB (Server Message Block)** → 445 (modern implementations, sometimes 139 for NetBIOS)  
- **LDAPS (Secure LDAP)** → 636 *(if relevant to interviews)*  
- **IMAPS (IMAP over TLS)** → 993  
- **POP3S (POP3 over TLS)** → 995  
- **RDP (Remote Desktop Protocol)** → 3389  
- **MySQL** → 3306 (registered) *(bonus from earlier exercises)*  
- **PostgreSQL** → 5432 *(bonus, common DB)*  
- **MSSQL** → 1433 *(bonus, Windows DB)*  

# 0.5.4 Common Protocols (TCP, UDP, HTTP, DNS, etc.)

## 1. TCP (Transmission Control Protocol)
- **Type:** Reliable, connection-oriented (3-way handshake).
- **Use cases:** HTTP(S), SMTP, IMAP, POP3, FTP.
- **Attacks:** SYN flood, session hijacking, RST injection.
- **Defenses:** SYN cookies, IDS/IPS, TLS for confidentiality & integrity.

---

## 2. UDP (User Datagram Protocol)
- **Type:** Unreliable, connectionless (faster, minimal overhead).
- **Use cases:** DNS queries, VoIP, streaming, gaming.
- **Attacks:** UDP floods, amplification (DNS, NTP, Memcached).
- **Defenses:** Rate limiting, filtering, anomaly detection.

---

## 3. HTTP / HTTPS
- **HTTP:** Web protocol (port 80), stateless.
- **HTTPS:** HTTP over TLS (port 443), encrypted & authenticated.
- **Common headers:** GET, POST, Host, Cookies, User-Agent.
- **Attacks:** MITM, sniffing, SQLi, XSS, CSRF, request smuggling.
- **Defenses:** TLS 1.2/1.3, secure cookies, input validation, WAF.

---

## 4. DNS (Domain Name System)
- **Purpose:** Translate domain names → IP addresses.
- **Ports:** UDP 53 (queries), TCP 53 (zone transfers).
- **Records:** A, AAAA, MX, TXT, CNAME, PTR, NS.
- **Attacks:** DNS spoofing/poisoning, tunneling (C2), amplification DDoS.
- **Defenses:** DNSSEC, split DNS, resolver monitoring.

---

## 5. Email Protocols
- **SMTP (25/587/465):** Send mail.
- **IMAP (143/993):** Retrieve (server sync).
- **POP3 (110/995):** Retrieve (download/delete).
- **Attacks:** Open relay abuse, phishing, credential theft.
- **Defenses:** SPF, DKIM, DMARC, enforce TLS.

---

## 6. SSH (Secure Shell)
- **Purpose:** Secure remote administration (port 22).
- **Attacks:** Brute-force logins, weak key mgmt, rogue daemons.
- **Defenses:** Disable password login, enforce SSH keys, restrict IPs, fail2ban.

---

## 7. SMB (Server Message Block)
- **Purpose:** File/print sharing (ports 139, 445).
- **Attacks:** EternalBlue (WannaCry), credential theft, SMB relay.
- **Defenses:** Block externally, patch, restrict shares, Kerberos auth.

---

## 8. ICMP (Internet Control Message Protocol)
- **Purpose:** Diagnostics (ping, traceroute, error reporting).
- **Attacks:** Ping floods, Smurf attacks, covert channels.
- **Defenses:** Rate limiting, restrict ICMP types, anomaly detection.

---

## 9. FTP (File Transfer Protocol)
- **Ports:** 20 (data), 21 (control).
- **Weakness:** Credentials and data sent in cleartext.
- **Attacks:** Brute force, credential theft, anonymous upload for malware.
- **Defenses:** Use FTPS/SFTP, disable anonymous logins, restrict access.

---

## 10. SNMP (Simple Network Management Protocol)
- **Ports:** 161/UDP (queries), 162/UDP (traps).
- **Purpose:** Network monitoring/management.
- **Weakness:** Default community strings (“public”, “private”).
- **Attacks:** Info disclosure, device control, SNMP amplification.
- **Defenses:** Use SNMPv3, restrict access to mgmt networks only.

---

## 11. RDP (Remote Desktop Protocol)
- **Port:** 3389/TCP.
- **Purpose:** Remote graphical access (Windows).
- **Attacks:** Brute-force, credential stuffing, ransomware entry vector.
- **Defenses:** MFA, restrict to VPN/internal, patch frequently, monitor login attempts.

---

## 12. NTP (Network Time Protocol)
- **Port:** 123/UDP.
- **Purpose:** Time synchronization.
- **Attacks:** NTP amplification DDoS, time-shift attacks for log/timestamp manipulation.
- **Defenses:** Restrict queries to trusted servers, rate limiting, authenticated NTP.

---

## 13. Interview One-Liners
- “TCP = reliable, UDP = fast but unreliable.”
- “HTTP is stateless, HTTPS secures with TLS.”
- “DNS translates names to IPs but is vulnerable to poisoning and tunneling.”
- “SMTP/IMAP/POP3 enable email but need SPF/DKIM/DMARC.”
- “SSH secures remote access, but brute force and weak keys are common risks.”
- “SMB critical in Windows networks, exploited by worms like WannaCry.”
- “ICMP is diagnostic but abused in DoS and covert channels.”
- “FTP is cleartext — always replace with SFTP/FTPS.”
- “SNMP with default community strings = easy target, use SNMPv3.”
- “RDP is ransomware’s favorite door — always restrict and use MFA.”
- “NTP can be weaponized in DDoS; restrict and authenticate sources.”

# 0.5.5 Network Addressing (IP, MAC, CIDR, NAT)

## 1. IP Addressing
- **IPv4:** 32-bit, dotted decimal (e.g., 192.168.1.1).
- **IPv6:** 128-bit, hexadecimal (e.g., 2001:db8::1).
- Split into **network portion** and **host portion**.
- **Public IPs:** Routable on internet.
- **Private IPs (RFC 1918):**
  - 10.0.0.0/8
  - 172.16.0.0/12
  - 192.168.0.0/16
- **Security Risks:** Public IPs exposed to internet scanning; attackers map ranges for recon.

---

## 2. MAC Addressing
- **MAC Address:** 48-bit hardware identifier burned into NIC.
- Format: `00:1A:2B:3C:4D:5E` (first 24 bits = vendor OUI).
- Works at **Layer 2 (Data Link)** for LAN communication.
- **Security Risks:** MAC spoofing, ARP spoofing for MITM.
- **Defenses:** Switch port security, 802.1X authentication.

---

## 3. CIDR (Classless Inter-Domain Routing)
- Notation like `/24` = number of network bits.
- Example: `192.168.1.0/24`
  - Network: 192.168.1.0
  - Broadcast: 192.168.1.255
  - Hosts: 192.168.1.1–192.168.1.254
- Replaces rigid Class A/B/C addressing.
- Enables efficient subnetting and IP allocation.
- **Security Angle:** Attackers scan whole CIDR blocks; defenders segment networks with CIDR (e.g., DMZ vs internal).

---

## 4. NAT (Network Address Translation)
- Maps private IPs to public IPs.
- **Types:**
  - Static NAT → 1:1 mapping.
  - Dynamic NAT → pool of public IPs.
  - PAT (Port Address Translation) → many-to-one (most common).
- Used in home networks, enterprises, ISPs.
- **Security Angle:** Hides internal IPs (basic obfuscation).
- Weakness: Misconfigured port forwarding can expose internal hosts.

---

## 5. Interview One-Liners
- “IP addresses identify devices across networks; MAC addresses identify devices on LAN.”
- “CIDR defines networks flexibly, replacing rigid A/B/C classes.”
- “NAT hides private networks behind public IPs but doesn’t replace a firewall.”
- “MAC spoofing = LAN attack; NAT misconfig = perimeter risk.”