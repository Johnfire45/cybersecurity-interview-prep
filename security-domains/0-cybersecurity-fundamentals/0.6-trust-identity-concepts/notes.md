# 0.6.1 Chain of Trust & Certificate Authorities

---

## 1. What is the Chain of Trust?
- Certificates prove identity of websites, servers, or entities.
- Trust is established through a **hierarchical chain**:
  - End-entity (leaf) certificate → Intermediate CA → Root CA.
- Browsers/OS don’t trust the leaf directly; they trust Root CAs pre-installed in their trust stores.

---

## 2. Certificate Hierarchy
1. **Root CA**
   - Self-signed.
   - Pre-installed in browsers/OS.
   - Highly secured (offline, HSM protected).

2. **Intermediate CA**
   - Issued by Root CA.
   - Adds a security layer; roots rarely sign end certs directly.
   - Issues end-entity certificates.

3. **End-Entity (Leaf) Certificate**
   - Issued to websites/services (e.g., `www.example.com`).
   - Must chain back to a trusted Root CA.

**Example Chain:**  
`www.example.com` → Intermediate CA → Root CA

---

## 3. Browser Trust Validation
- Certificate signed by a trusted CA.
- Cryptographic signature is valid.
- Certificate is not expired or revoked.
- Hostname matches CN/SAN in the cert.
- Chain links back to a trusted Root CA in the trust store.

If any check fails → browser shows *“Your connection is not private”*.

---

## 4. Security Risks & Attacks
- **Compromised CAs:** Fake certs issued (e.g., DigiNotar hack).
- **Rogue certs:** Issued under coercion or insider threat.
- **Expired certs:** Cause outages (e.g., Azure, major service downtimes).
- **Weak hashing (MD5/SHA1):** Allow forgery/collision attacks.

---

## 5. Defenses & Best Practices
- Use strong crypto (RSA-2048/ECC, SHA-256+).
- Deploy **certificate pinning** (trust specific certs).
- Use **OCSP stapling** or CRLs for revocation checks.
- Automate certificate renewal (e.g., Let’s Encrypt).
- Monitor **Certificate Transparency (CT) logs** for rogue certs.

---

## Interview One-Liners
- “Chain of trust links a website’s cert to a trusted Root CA in the OS/browser trust store.”
- “Root CAs are offline; intermediates issue real-world certs.”
- “CA compromise = fake cert issuance; expired certs = outages.”
- “Defenses: pinning, revocation checking, CT log monitoring.”

flowchart TD
    A[Root CA<br>(Trusted in OS/Browser)] --> B[Intermediate CA<br>(Signed by Root)]
    B --> C[End-Entity Certificate<br>(www.example.com)]
    C --> D[Browser/OS Verification<br>Checks signature, expiry, hostname]

    subgraph TLS Handshake
        E[ClientHello<br>(Propose TLS params)] --> F[ServerHello<br>(Chosen TLS params)]
        F --> G[Server sends Certificate Chain<br>(Leaf + Intermediate + Root ref)]
        G --> H[Browser validates Chain<br>(Leaf → Intermediate → Root)]
        H --> I[Key Exchange (Diffie-Hellman/ECDHE)]
        I --> J[Secure Channel Established<br>(All traffic encrypted)]
    end

    D --> H