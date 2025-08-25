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

    # 0.6.2 Encryption (Symmetric & Asymmetric)

---

## 1. Symmetric Encryption
- **Definition**: Same key used for both encryption and decryption.
- **Algorithms**: AES (standard), ChaCha20 (TLS 1.3), DES/3DES (deprecated).
- **Modes**:
  - ECB ❌ insecure, leaks patterns.
  - CBC → adds IV, better but padding needed.
  - GCM ✅ modern, provides encryption + integrity.
- **Strengths**:
  - Very fast → ideal for bulk data (VPN, HTTPS traffic, file encryption).
- **Weaknesses**:
  - Key distribution problem (how to share key securely).
  - Scalability issue → O(N²) keys for N users.

---

## 2. Asymmetric Encryption
- **Definition**: Uses key pair → Public key (encrypt/verify), Private key (decrypt/sign).
- **Algorithms**: RSA, ECC, Diffie-Hellman/ECDH.
- **Strengths**:
  - Solves key distribution problem.
  - Enables digital signatures, non-repudiation, authentication.
- **Weaknesses**:
  - Slower and computationally expensive.
  - Relies on trust binding (certificates).
  - Weak key sizes (RSA-1024) vulnerable.

---

## 3. Hybrid Encryption (Real-World)
- **Why hybrid?**: Symmetric = fast, Asymmetric = secure distribution.
- **TLS Handshake**:
  1. Client & server exchange params.
  2. Server sends certificate with public key.
  3. Browser generates session key → encrypts with server’s public key.
  4. Server decrypts with private key.
  5. Both use session key for AES-GCM/ChaCha20 traffic.
- **Other uses**: PGP, disk encryption.

---

## 4. Security Mapping
- **OWASP A02: Cryptographic Failures**:
  - Weak/legacy algorithms (DES, RC4, MD5, SHA-1).
  - Hardcoded keys.
  - No key rotation / reused IVs.
  - Invalid or unchecked cert chains.
- **Defenses**:
  - Use AES-256, RSA-2048+, ECC-256+.
  - Modern modes (AES-GCM, ChaCha20-Poly1305).
  - Protect keys in HSMs/vaults.
  - Automate rotation, renewal.

---

## Interview One-Liners
- “Symmetric = one key, fast but key distribution is hard.”
- “Asymmetric = key pair, solves distribution but slower.”
- “TLS uses asymmetric for key exchange, symmetric for bulk data.”
- “Cryptographic failures (OWASP A02) → weak ciphers, poor key mgmt.”

# 0.6.3 Digital Signatures

---

## 1. What is a Digital Signature?
- Cryptographic equivalent of a handwritten signature or wax seal.
- Provides:
  - **Authenticity** → message came from claimed sender.
  - **Integrity** → message wasn’t altered.
  - **Non-repudiation** → sender cannot deny sending.

---

## 2. How Digital Signatures Work
1. **Message Hashing**  
   - Sender creates a hash (e.g., SHA-256) of the message.
2. **Signing**  
   - Sender encrypts hash with their **private key** → digital signature.
3. **Transmission**  
   - Sender sends message + signature.
4. **Verification**  
   - Receiver computes hash of message.
   - Receiver decrypts signature with sender’s **public key** to retrieve original hash.
   - If hashes match → message authentic and intact.

---

## 3. Algorithms
- **RSA** → private key signs, public key verifies.
- **ECDSA** → elliptic curve, faster, smaller key sizes.
- **EdDSA (Ed25519)** → modern, secure, high performance.

---

## 4. Real-World Applications
- **Code Signing** (Microsoft, Apple packages).
- **TLS Certificates** (CA signs certs).
- **Email Security** (S/MIME, DKIM).
- **Blockchain** (crypto transactions).
- **Documents** (digitally signed PDFs, contracts).

---

## 5. Attacks & Failures
- **Private key compromise** → forged signatures.
- **Weak hashes** (MD5, SHA-1) → collisions → fake signatures.
- **Improper verification** → skipping validation (JWT, XMLDSig flaws).

---

## 6. Security Mapping
- **OWASP A02: Cryptographic Failures** → weak or unchecked signatures.
- **Best Practices**:
  - Use SHA-256+ or SHA-3.
  - Use RSA-2048+, ECC (P-256, Ed25519).
  - Protect private keys (HSMs, vaults).
  - Rotate/revoke compromised keys.

---

## Interview One-Liners
- “Encryption = confidentiality; Digital signature = integrity, authenticity, non-repudiation.”
- “TLS: server signs handshake transcript with private key; client verifies with public key.”
- “Signature security depends on strong hashing + secure key management.”

# 0.6.4 Key Management & PKI

---

## 1. What is PKI?
- **Public Key Infrastructure (PKI)** is the framework that manages keys and digital certificates.
- Provides identity, trust, and secure communication.
- Manages the lifecycle of keys: generation, distribution, storage, rotation, revocation, and destruction.

---

## 2. Core Components
- **Certificate Authority (CA)**
  - Root CA: trust anchor, usually offline, signs intermediates.
  - Intermediate CA: issues end-entity certificates.
- **Registration Authority (RA)**
  - Verifies identities before certificate issuance.
- **Certificates (X.509)**
  - Bind a public key to an identity (domain, org, user).
- **Certificate Revocation**
  - CRL (Certificate Revocation List).
  - OCSP (Online Certificate Status Protocol).
- **Key Pairs**
  - Public key (shared openly).
  - Private key (must remain secret).

---

## 3. Key Management Lifecycle
- **Generation** → strong algorithms, secure entropy.
- **Distribution** → secure delivery (PKI, TLS).
- **Storage** → HSMs, TPMs, vaults (never plaintext).
- **Rotation** → periodic renewal.
- **Revocation** → revoke compromised keys/certs.
- **Destruction** → secure deletion of retired keys.

---

## 4. Real-World Use Cases
- **TLS/HTTPS** → CA-issued certificates.
- **Email Security** → S/MIME, PGP.
- **Code Signing** → software vendor signatures.
- **Enterprise/VPNs** → client/server authentication.
- **IoT** → device certificates for identity.
- **Zero Trust** → short-lived certificates.

---

## 5. Common Failures
- Storing private keys insecurely (repos, plaintext).
- Missing rotation/revocation processes.
- CA compromise (e.g., DigiNotar 2011).
- Expired certificates causing outages (e.g., Equifax).
- Weak crypto (RSA-1024, SHA-1).

---

## 6. Security Mapping
- **OWASP A02: Cryptographic Failures**:
  - Weak keys, missing cert validation, expired certs.
- **Best Practices**:
  - Use RSA-2048+/ECC (P-256, Ed25519).
  - Automate lifecycle (ACME, Let’s Encrypt).
  - Secure storage in HSMs/vaults.
  - Monitor expiry, enforce short validity.

---

## Interview One-Liners
- “PKI ties public keys to identities through trusted certificates.”
- “Root CA = trust anchor; Intermediates issue certs.”
- “Private keys must be stored in HSMs or vaults, never plaintext.”
- “Expired or compromised certificates can cause global outages.”

Real-World Example in Context
	•	PKI + AuthN + AuthZ in action (Corporate VPN):
	1.	User authenticates with MFA + certificate (AuthN → proven identity).
	2.	PKI validates the cert against trusted CA (Trust & Identity check).
	3.	Once inside, RBAC restricts the user’s role (AuthZ → allowed actions).
	4.	If either AuthN or AuthZ breaks → attacker gains access or escalates privileges.