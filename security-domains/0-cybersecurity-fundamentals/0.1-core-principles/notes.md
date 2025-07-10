# CIA Triad (Confidentiality, Integrity, Availability)

## 📘 Overview

The **CIA Triad** is the foundational model for information security, consisting of:
- **Confidentiality**: Ensuring that only authorized individuals can access information.
- **Integrity**: Guaranteeing the accuracy and consistency of data over its lifecycle.
- **Availability**: Ensuring information and systems are accessible when needed.

This model guides the design, evaluation, and implementation of security policies across all domains—whether network, application, or data security.

## 🎯 Attack Scenarios

- **Confidentiality breach**: Misconfigured cloud storage (e.g., public S3 buckets) leaking sensitive employee payroll data.
- **Integrity violation**: Tampering of financial logs by insiders using `.innerHTML` manipulation or file upload naming bypass.
- **Availability attack**: Denial-of-service (DoS) on critical infrastructure causing downtime during peak business hours.

Advanced example:
- A user overwriting HR documents by uploading a file with the same name and bypassing file validation (breaks both **integrity** and **availability**).

## 🧪 Detection Techniques

- **Confidentiality**: Audit logs, access review reports, and DLP (Data Loss Prevention) alerts.
- **Integrity**: Hash/checksum verification, file integrity monitoring tools (e.g., Tripwire).
- **Availability**: Uptime monitoring tools, SIEM alerts on service crashes or traffic spikes.

## 🛠️ Exploitation Flow

Example (Integrity):
1. User uploads a malicious file with the same name as a critical document.
2. Application lacks file name collision protection or file hash check.
3. Original file is silently replaced — integrity lost.

Example (Confidentiality):
1. HR misconfigures database access permissions.
2. Internal network allows all users to view sensitive records.
3. Unauthorized data access occurs.

## 🔐 Mitigation Strategies

- **Confidentiality**
  - Implement Role-Based Access Control (RBAC)
  - Enforce principle of least privilege
  - Encrypt sensitive data in transit and at rest

- **Integrity**
  - Use cryptographic hashes (SHA256) for file verification
  - Apply digital signatures
  - Log and monitor all file modification actions

- **Availability**
  - Use DDoS protection mechanisms (e.g., rate limiting, WAF)
  - Ensure redundancy and failover systems
  - Monitor service uptime with alerting

## 🔗 References & Resources

- [OWASP Top 10](https://owasp.org/Top10/)
- [NIST SP 800-12 Rev. 1: Introduction to Information Security](https://csrc.nist.gov/publications/detail/sp/800-12/rev-1/final)
- [Wikipedia: CIA Triad](https://en.wikipedia.org/wiki/Information_security#The_CIA_triad)

# Threat Modeling

## 📘 Overview

Threat modeling is a **proactive security design practice** where engineers and security teams identify potential threats to a system *before* it’s deployed. The core idea is to ask:  
**"What are we building? What can go wrong? What are we doing about it?"**

It helps uncover:
- Design flaws
- Trust boundary violations
- Missing access controls
- Unhandled threat surfaces

Widely used in DevSecOps, cloud-native architectures, and regulatory compliance efforts.

---

## 🎯 Attack Scenarios

- 🧨 **Trust Boundary Violation**: File upload flows bypass MIME checks, triggering SSRF or RCE in backend parsers.
- 🧨 **Overprivileged Microservices**: Compromised service with hardcoded tokens can invoke admin endpoints on peer services.
- 🧨 **Missing Auth in Internal APIs**: Developers assume internal networks are safe. Attackers pivot via SSRF or insecure VPNs.

---

## 🧪 Detection Techniques

- Conduct **Threat Modeling Workshops** (during design phase)
- Use **Data Flow Diagrams (DFDs)** to visualize trust boundaries, entry/exit points
- Review for:
  - Missing input validation at trust boundaries
  - Overly permissive service communications
  - Unauthenticated critical endpoints

---

## 🛠️ Exploitation Flow

1. Attacker enumerates exposed API or upload endpoint.
2. Finds lack of validation/sanitization across boundary (e.g., web → backend).
3. Crafts input (e.g., SSRF payload, auth bypass) to exploit assumptions.
4. Gains unauthorized access or escalates privilege via misconfig.

---

## 🔐 Mitigation Strategies

- **STRIDE**-based threat modeling for each component
- Define **trust boundaries** clearly in architecture diagrams
- Use **service-level authentication** (mTLS, JWT claims)
- Enforce **input validation** at all exposed interfaces
- Apply **least privilege principles** between services/modules
- Treat threat modeling as **continuous**, not one-time

---

## 🧠 Frameworks Summary

| Model   | Use Case                          |
|---------|-----------------------------------|
| **STRIDE**  | Security threat classification    |
| **LINDDUN** | Privacy-focused threat modeling   |
| **DREAD**   | Deprecated threat scoring system  |

---

## 🔍 Real-World Examples

### 🧨 Broken File Upload Trust
- No validation at upload boundary
- Parser executes embedded payload
- 🔒 Fix: Filetype whitelisting, sandboxing backend parser

### 🧨 Implicit Microservice Trust
- Microservice B assumes Microservice A is always trusted
- A is compromised → attacker gets access to B
- 🔒 Fix: Add internal service auth, limit token scope

---

## 📘 1.3.2 Threat Modeling — Key Components

Before applying a threat modeling framework, it’s important to understand the **core components** involved in any effective threat modeling exercise:

---

### 🧱 Core Components

1. **Assets**
   - What needs protection? (e.g., PII, credentials, payment data, services)
   - Can include users, infrastructure, and software components

2. **Actors**
   - Who interacts with the system? (e.g., users, attackers, third-party services)
   - Includes both internal and external threat agents

3. **Entry Points**
   - Where can interaction begin? (e.g., APIs, forms, upload portals)
   - These are often the attack surface

4. **Trust Boundaries**
   - Logical points where different levels of trust meet (e.g., client ↔ backend, frontend ↔ DB)
   - Crucial for identifying validation gaps and privilege boundaries

5. **Data Flows**
   - How data moves between components
   - Used to generate **Data Flow Diagrams (DFDs)** for visual modeling

6. **Security Controls**
   - Existing defenses (e.g., input validation, authentication, logging, rate limiting)
   - Basis for identifying gaps

---

## 🧠 Threat Modeling Frameworks

A range of structured approaches exist to guide the modeling process depending on the goal: security, privacy, risk analysis, or agility.

---

### 🧠 1. STRIDE (Microsoft)
- **Purpose**: Identify threats in system design by classifying them into 6 categories:
  - **S**poofing
  - **T**ampering
  - **R**epudiation
  - **I**nformation Disclosure
  - **D**enial of Service
  - **E**levation of Privilege
- 🔍 Strongly supported in the file using **DFDs**, **trust boundaries**, and threat identification
- 🧩 Best for: Design-phase threat identification in app/system architecture

---

### ⚙️ 2. PASTA (Process for Attack Simulation and Threat Analysis)
- **Purpose**: Risk-centric and attacker-focused framework to simulate realistic attack paths
- **7 Stages**: 
  1. Define business objectives  
  2. Define technical scope  
  3. Application decomposition  
  4. Threat analysis  
  5. Vulnerability analysis  
  6. Attack modeling  
  7. Risk and impact analysis
- 📈 Maps well to SDLC stages and threat intelligence
- 🧩 Best for: Large-scale systems, high-risk environments, and regulatory alignment

---

### 🛡️ 3. LINDDUN
- **Purpose**: Privacy-focused threat modeling
- Focus Areas:
  - **L**inkability, **I**dentifiability, **N**on-repudiation, **D**etectability, **D**isclosure of information, **U**nauthorized actions, **N**on-compliance
- 🔍 Mentioned in the framework table
- 🧩 Best for: Systems handling PII or subject to privacy regulations (e.g., GDPR)

---

### 🛑 4. DREAD (Deprecated)
- **Purpose**: Scoring system for threat prioritization (e.g., Damage, Reproducibility, Exploitability...)
- ❗Marked as deprecated and no longer widely recommended
- 🔍 Included in table for legacy awareness
- 🧩 Not advised for modern use, but may appear in legacy systems/interview discussions

---

## ✅ Summary Table

| Model   | Use Case                          |
|---------|-----------------------------------|
| **STRIDE**  | Security threat classification    |
| **PASTA**   | Risk-driven attack simulation     |
| **LINDDUN** | Privacy-focused threat modeling   |
| **DREAD**   | Deprecated threat scoring system  |

## 🛡️ Interview Notes

- Expect to **draw a data flow diagram** and identify STRIDE threats per element
- Be ready to **map real-world vulnerabilities** to trust boundary issues
- Highlight **design-phase security mindset** in SDLC interviews