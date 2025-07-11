
# [CIA Triad]

## 🧪 Quiz

1. **Question:**  
   A multi-tenant cloud-based HR platform revealed the following security issues during a pentest:

   - **Confidentiality Breach**: JWT token intercepted over an unencrypted API allowed impersonation of another tenant's admin and access to their employee records.
   - **Integrity Violation**: A malicious user could upload a file with the same name (`payroll_q1.csv`) to overwrite another tenant’s payroll data.
   - **Availability Failure**: A spike in log volume caused a Redis-based audit logging service to crash, locking out all admin logins for 3 hours.

   As a security engineer, analyze and map each violation to the appropriate CIA Triad component. For each:
   - State the cause
   - Describe the impact
   - Propose suitable mitigations
   - Mention the design flaw or control that was missing

   **Answer:**  
   - **Confidentiality**:
     - *Cause*: JWT passed over unencrypted channel; no token scoping; lack of RBAC and IP binding.
     - *Impact*: Unauthorized access to tenant-specific employee records.
     - *Mitigation*: Enforce HTTPS, bind JWTs to session/IP/device, validate roles per API.
     - *Flaw*: Lack of secure transport and token-level access validation.

   - **Integrity**:
     - *Cause*: No file ownership validation; overwriting via filename.
     - *Impact*: Malicious user overwrites payroll data of another tenant.
     - *Mitigation*: Use UUID-based file naming, validate ownership, enable version control.
     - *Flaw*: No access control or integrity check on file upload/update.

   - **Availability**:
     - *Cause*: Redis was a single point of failure; no tenant-level rate control.
     - *Impact*: Audit logs crashed Redis, taking down admin login system.
     - *Mitigation*: Isolate tenants via quotas, introduce circuit breakers, use Redis clustering.
     - *Flaw*: No resilience or load protection in backend design.


# [Threat Modeling]

## 🧪 Quiz

1. **Question:**  
   You are conducting a STRIDE-based threat modeling exercise for a multi-tenant B2B SaaS analytics platform. The system includes a React frontend, Node.js GraphQL backend, Redis, and PostgreSQL. During review, you discover:

   - Billing microservice **trusts JWTs** from the reporting service without verifying signature or audience  
   - GraphQL **introspection is enabled** in production  
   - No **row-level access control** in PostgreSQL — only application-side filters  
   - Public APIs lack **rate limiting or bot protection**

   **Task:**  
   Map these issues to STRIDE categories. For each (S, T, R, I, D, E):
   - Identify the threat  
   - Propose a mitigation  
   - Mention the broken design principle

   *Bonus: Identify any missing trust boundaries.*

   **Answer Summary:**
   - **Spoofing**: JWTs are accepted without validation → allows impersonation  
     → *Mitigation*: Verify signature, bind to session/device/IP  
   - **Tampering**: JWT and tenant ID can be altered → data/role tampering  
     → *Mitigation*: Signed tokens, claim validation  
   - **Repudiation**: Actions can’t be traced if JWT is forged  
     → *Mitigation*: Logging + signed, verifiable tokens  
   - **Information Disclosure**: GraphQL introspection leaks schema in prod  
     → *Mitigation*: Disable introspection in production  
   - **Denial of Service**: No rate limiting or auth throttling  
     → *Mitigation*: API rate limits, CAPTCHA, circuit breakers  
   - **Elevation of Privilege**: Modifying token or tenant ID grants admin access  
     → *Mitigation*: Enforce RBAC, scope-based token checks

   **Trust Boundary Missed:**  
   - Microservices trust internal traffic blindly  
   - DB assumes app-layer will enforce tenant separation  
   - Public frontend not throttled or isolated

   
### 🔐 Security Principles — Advanced Scenario Quiz

#### 🧠 Scenario: Multi-Principle Security Breakdown in Fintech Integration

A fintech startup integrates with a third-party payment gateway. Here’s what happened in a real-world incident:

- Internal microservices are protected via a centralized API gateway, but **each microservice blindly trusts JWTs** without verifying signatures.
- The payment module **encrypts card tokens**, but **stores the encryption key hardcoded** in the source code — which was accidentally **pushed to a public GitHub repo**.
- An attacker finds an **exposed CI/CD backup folder** on a cloud bucket containing:
  - `.env` files with API secrets
  - Admin access tokens (expired, but **not revoked**)
  - Source code files exposing internal microservice routes
- The application has **no rate limiting** or behavioral anomaly detection.
- **All authenticated users**, including **interns**, can invoke an **internal diagnostic API** that dumps logs with **PII and credentials**.
- Developers assume “no one will find these routes,” using **security through obscurity**.

---

#### ❓Question:

**Which FOUR Security Principles were violated?**  
Explain **each violation** in context of the above scenario.

---

#### ✅ Expected Answer (Reference)

**1. Defense in Depth**
- No layered protection (JWT blindly trusted, no rate limits, no anomaly detection).
- If one layer failed (e.g., gateway), no fallback validation existed.

**2. Least Privilege**
- Interns could access diagnostic APIs and log sensitive data.
- CI/CD folders were publicly readable — permissions were not scoped.

**3. Fail-Safe Defaults**
- Sensitive systems and logs were accessible without explicit permission boundaries.
- Assumes access is allowed unless explicitly denied.

**4. Open Design**
- Sensitive keys were hardcoded in code pushed to GitHub.
- Assumed obscurity (undocumented routes) would keep attackers out.
- Security relied on secrecy, not on robust, reviewed design.

---

### ❓Question: for blue,read and purple team

You’re hired as a security consultant for a **fintech company** that recently suffered a **sophisticated phishing attack**, followed by **lateral movement** and **data exfiltration**.

The **CTO wants to build out Red, Blue, and Purple Teams** to prevent such incidents in the future.

---

🔍 **How would you design a collaborative workflow between these three teams to detect, prevent, and respond to such attacks?**

In your answer, detail:

- ✅ Specific tasks each team would perform  
- 🔁 How intel and outcomes should flow between them  
- 🛠️ What tooling or frameworks (e.g., **MITRE ATT&CK**, **SIEMs**, **emulation tools**) would be critical for success  
- 🎯 How success would be measured

---

### ✅ Answer:

- **Red Team**
  - *Tasks*:
    - Simulate phishing campaigns (e.g., spear-phishing emails, social engineering).
    - Execute post-exploitation steps like privilege escalation, lateral movement, and data exfiltration simulations.
    - Identify exploitable gaps in email security, endpoint defense, and employee awareness.
  - *Intel Produced*:
    - Attack paths, exploited misconfigurations, credential theft patterns.
    - TTPs mapped to MITRE ATT&CK.
    - Timeline of compromise activities.

- **Blue Team**
  - *Tasks*:
    - Monitor email gateways, endpoints, SIEM logs, and network behavior for anomalous activity.
    - Detect phishing attempts, credential use anomalies, lateral movement signatures.
    - Respond to Red Team simulations by isolating systems, revoking tokens, investigating alerts.
  - *Intel Consumed*:
    - Red Team’s TTPs used for detection rule development.
    - Continuous alert tuning based on simulation results.

- **Purple Team**
  - *Tasks*:
    - Coordinate attack-defense simulations and exercises.
    - Facilitate knowledge sharing between Red and Blue Teams.
    - Translate Red Team findings into detection logic, playbooks, and Blue Team improvements.
    - Drive post-mortem reviews, simulation feedback loops, and capability maturity assessments.
  - *Intel Role*:
    - Acts as bidirectional bridge: converts Red Team findings into Blue Team detection/response mechanisms.
    - Documents lessons learned and prioritizes defensive backlog.

---

### 🛠️ Tooling & Frameworks

- **MITRE ATT&CK**: Used to map Red Team TTPs and track detection coverage.
- **SIEM Platforms**: ELK, Splunk, Sentinel for log analysis, correlation, alerting.
- **Emulation Tools**: AIMS, Atomic Red Team, Caldera for safe adversarial testing.
- **EDR/XDR**: CrowdStrike, SentinelOne to detect lateral movement and persistence.
- **Security Automation**: SOAR playbooks to reduce response time.
- **CIEM/CSPM**: For hardening cloud-based IAM and policies.

---

### 🎯 Success Metrics

- Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR) improvements.
- Percentage of Red Team TTPs successfully detected and blocked.
- Reduction in false positives and false negatives in alerts.
- Increased phishing resistance scores in employee simulations.
- Number of new detections, rules, or playbooks authored from Purple Team facilitation.
> 🧠 This question integrates multiple real-world violations and tests understanding of layered architectural risks, design flaws, and access control principles.