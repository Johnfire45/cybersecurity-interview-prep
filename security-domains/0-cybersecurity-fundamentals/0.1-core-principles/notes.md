```markdown
# CIA Triad (Confidentiality, Integrity, Availability)

## 📘 Overview

The **CIA Triad** is the foundational model for information security, consisting of:
- **Confidentiality**: Ensuring that only authorized individuals can access information.
- **Integrity**: Guaranteeing the accuracy and consistency of data over its lifecycle.
- **Availability**: Ensuring information and systems are accessible when needed.

This model guides the design, evaluation, and implementation of security policies across all domains—whether network, application, or data security.

---

## 🎯 Attack Scenarios

- **Confidentiality breach**: Misconfigured cloud storage (e.g., public S3 buckets) leaking sensitive employee payroll data.
- **Integrity violation**: Tampering of financial logs by insiders using `.innerHTML` manipulation or file upload naming bypass.
- **Availability attack**: Denial-of-service (DoS) on critical infrastructure causing downtime during peak business hours.

Advanced example:
- A user overwriting HR documents by uploading a file with the same name and bypassing file validation (breaks both **integrity** and **availability**).

---

## 🧪 Detection Techniques

- **Confidentiality**: Audit logs, access review reports, and DLP (Data Loss Prevention) alerts.
- **Integrity**: Hash/checksum verification, file integrity monitoring tools (e.g., Tripwire).
- **Availability**: Uptime monitoring tools, SIEM alerts on service crashes or traffic spikes.

---

## 🛠️ Exploitation Flow

Example (Integrity):
1. User uploads a malicious file with the same name as a critical document.
2. Application lacks file name collision protection or file hash check.
3. Original file is silently replaced — integrity lost.

Example (Confidentiality):
1. HR misconfigures database access permissions.
2. Internal network allows all users to view sensitive records.
3. Unauthorized data access occurs.

---

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

---

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

## 🧱 Core Components of Threat Modeling

- **Assets**: What needs protection? (e.g., data, services, credentials)
- **Attackers**: Who can threaten the system? (internal, external, automated)
- **Trust Boundaries**: Where trust levels shift (e.g., client ↔ server)
- **Entry/Exit Points**: Where interaction begins or ends (APIs, forms, inputs)
- **Threats**: What can go wrong across components or flows
- **Mitigations**: How to prevent, detect, or contain identified threats

---

## ⚔️ Threat Modeling Frameworks

| **STRIDE**               | **PASTA**                             | **LINDDUN**                                               | **OCTAVE**                                    |
|--------------------------|----------------------------------------|------------------------------------------------------------|-----------------------------------------------|
| Spoofing                 | Process for Attack Simulation         | Linkability                                                | Operational Risk                              |
| Tampering                | Threat Analysis                       | Identifiability                                            | Critical Threat Assets                        |
| Repudiation              | Attack Vectors                        | Non-Repudiation                                            | Vulnerability Evaluation                      |
| DoS                      | Network Simulation                    | Detectability                                              |                                               |
| Privilege Escalation     |                                        | Disclosure                                                 |                                               |
|                          |                                        | Unawareness                                                |                                               |
|                          |                                        | Non-Compliance                                             |                                               |
| ✅ Used For:             | ✅ Used For:                           | ✅ Used For:                                               | ✅ Used For:                                   |
| Web/Mobile/API           | Complex Enterprise Risk Modeling      | Privacy-Focused Systems                                    | Internal Business Risk Modeling               |
| Thick Client             |                                        |                                                            |                                               |

---

### 📌 Usage Tips

- 🧠 Combine **STRIDE + PASTA** for layered threat + risk modeling
- 🔐 Apply early in SDLC: architecture design, CI/CD pipelines
- 🔍 LINDDUN helps for **privacy** risks (e.g., GDPR, PII)
- 📊 OCTAVE is **asset and business-impact driven**
- Treat threat modeling as **continuous**, not one-time

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

- Define **trust boundaries** clearly in architecture diagrams
- Use **service-level authentication** (mTLS, JWT claims)
- Enforce **input validation** at all exposed interfaces
- Apply **least privilege principles** between services/modules
- Use **STRIDE** or other models to drive secure design reviews
- Make threat modeling a **recurring process**

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

## ✅ Summary Table

| Model   | Use Case                          |
|---------|-----------------------------------|
| **STRIDE**  | Security threat classification    |
| **PASTA**   | Risk-driven attack simulation     |
| **LINDDUN** | Privacy-focused threat modeling   |
| **OCTAVE**  | Business and asset risk modeling  |
| **DREAD**   | ⚠️ Deprecated threat scoring model |

---
```

## 🔐 Security Principles

> **Fundamental guidelines** on how to **design, implement & access secure systems**.

### 🎯 Mastering Them Helps You:
- Critically evaluate system designs
- Spot vulnerabilities early
- Architect secure solutions

---

### 🧱 Essential Security Principles

| 🔑 **Principle**               | 🎯 **Purpose**                                                                 | 🛡️ **Key Benefits**                                                  |
|-------------------------------|-------------------------------------------------------------------------------|----------------------------------------------------------------------|
| **Least Privilege**           | Minimize rights/access to users/processes                                     | 🔻 Reduces Attack Surface                                            |
| **Defense-in-Depth**          | Multiple overlapping defense layers                                           | 🧱 Resilience to Layered Attacks                                     |
| **Fail-Safe Defaults**        | Deny access by default                                                        | ❌ Minimizes Unintentional Access                                    |
| **Separation of Duties**      | Divide critical tasks between roles                                           | 🔐 Prevents Error or Abuse from Single User                          |
| **Economy of Mechanism**      | Simple logic → minimal errors → simpler attack surface                        | ✅ Easier to Audit & Secure                                          |
| **Open Design**               | Don't rely on secrecy; publish secure design                                 | 🌍 Encourages Transparency & Robustness                              |
| ~~Security Through Obscurity~~ ⚠️ | ⚠️ **Myth**: “No one can guess this...” → weak assumption               | 🚫 Never substitute for real security controls                       |
| **Complete Mediation**        | Every access must be verified against the access control policy               | 🛑 Stops Permission Leakage<br>❗ Don't cache permissions             |
| **Least Common Mechanism**    | Avoid shared memory/resources across different privilege levels               | ⚠️ Limits Inter-process Abuse<br>🧯 Prevents Cross-Impact             |
| **Psychological Acceptability** | Security must not hinder legitimate users                                    | 🧠 Encourages Compliance<br>🟠 Overly complex design → weak behaviors |

> Example: Forcing users to remember a 256-bit hex password → weak password reuse or sticky notes.  

## 🛡️ Red Team vs Blue Team vs Purple Team

## 🔄 Comparison Table

| 🧩 Category            | 🔴 Red Team (Offensive)                                      | 🔵 Blue Team (Defensive)                                         | 🟣 Purple Team (Collaborative)                                    |
|------------------------|--------------------------------------------------------------|------------------------------------------------------------------|------------------------------------------------------------------|
| **Role**               | Emulates attackers                                           | Detects & defends                                                | Bridges Red & Blue teams                                         |
| **Objective**          | Identify unknown vulnerabilities                             | Minimize attack surface & incident impact                        | Improve defense through collaboration                           |
| **Primary Focus**      | Attacks, exploitation, evasion                               | Monitoring, detection, and response                              | Coordination, tuning, feedback loops                            |
| **Core Activities**    | Social engineering, lateral movement, adversary simulation   | Threat hunting, SIEM analysis, incident response                 | Exercise facilitation, detection gap analysis                   |
| **Key Tools/Concepts** | MITRE ATT&CK, C2 frameworks, exploits                        | SIEM, endpoint monitoring, network logs                          | MITRE coverage, adversary emulation, detection mapping          |
| **Mindset**            | "Assume breach is possible — prove it"                       | "Assume breach happened — catch it fast"                         | "Assume breach is continuous — build synergy between teams"     |
| **Interview Tip**      | Demonstrate how you chain vulnerabilities                   | Show how you detect, triage, and respond to alerts               | Explain how findings improve defense mechanisms                 |
| **Real-World Analogy** | Penetration tester or ethical hacker                         | SOC analyst or detection engineer                                | Purple team lead or threat simulation engineer                  |

---

> ✅ **Interview Guidance:**  
- Red teamers often get tested on chaining exploits, lateral movement, and bypassing detection.  
- Blue teamers are quizzed on log analysis, triage, and SIEM correlation use cases.  
- Purple team interviews often assess your ability to simulate threats and measure detection gaps.