# 📄 File: quiz.md
# Risk-Mangement-Frameworks
---

### 🧨 Real-World Scenario

**You’ve joined a large healthcare SaaS company as a Security Engineer.** Within your first month, you uncover:

- Patient PII is stored in **S3 buckets with overly permissive IAM policies**.
- **No formal risk register** exists, and responses to incidents are reactive.
- Developers have **direct access to production systems**.
- The company is preparing for **HIPAA compliance** and **ISO 27001 certification**.

The CISO tasks you to design a **Risk Management Framework** that:

- Identifies and prioritizes risks
- Aligns with **ISO 27005** and **HIPAA**
- Enables cross-functional collaboration
- Supports audit readiness

---

### 🎯 Question

**How would you go about designing and implementing this Risk Management Framework?**

Your answer must include:

- 🧩 Risk Identification & Classification  
- 🔎 Risk Assessment Techniques  
- 🛠️ Risk Treatment Plan  
- 🗂️ Tools and Deliverables  
- 🔁 Feedback Loop  
- 🧪 Mapping to ISO 27005 & HIPAA  
- 📊 Metrics for Success

---

### ✅ Answer

For designing this risk management framework, we need to understand all of our threats and the risks that are present. We must first classify those risks appropriately.

From the scenario:

- The **overly permissive IAM policies** on S3 buckets imply that **unauthorized access** to sensitive patient data is possible.
- The absence of a **formal risk register** leads to **reactive security**, lacking structured governance.
- **Direct production access** for developers is critical — it increases insider risk and violates least privilege. If a developer's system is compromised, an attacker could potentially exfiltrate production data or bring down services.

### 🧩 Risk Identification & Classification

- **Data Exposure**: Sensitive patient data stored insecurely (PII risk).
- **Access Control Risks**: IAM misconfigurations, overly permissive roles.
- **Insider Threat**: Developer access to production without segmentation or controls.
- **Compliance Gaps**: No formal documentation, policies, or procedures.

### 🔎 Risk Assessment Techniques

- **Qualitative assessment**: Classify based on probability (High/Med/Low) and impact (High/Med/Low).
- **Quantitative assessment** (if data is available): Estimate potential cost of breach, likelihood of exploitation.

### 🛠️ Risk Treatment Plan

- **Mitigation**:  
  - Restrict IAM roles via least privilege  
  - Revoke direct prod access for developers  
  - Introduce break-glass access mechanisms  
- **Acceptance**: Minor risks that are low-impact and low-likelihood.
- **Transfer**: Consider insurance where applicable.
- **Avoidance**: Retire services that pose excessive risk.

### 🗂️ Tools & Deliverables

- **Risk Register** (e.g., spreadsheet, GRC platform): Document threat, asset, risk rating, treatment, owner, due date.
- **Mapping** to ISO 27005 Risk Lifecycle:
  - Risk identification  
  - Risk analysis  
  - Risk evaluation  
  - Risk treatment  
  - Risk monitoring & review

### 🔁 Feedback Loop

- Embed risk assessment in **SDLC and CI/CD pipelines**
- Schedule regular risk reviews (quarterly)
- Enable real-time alerting via **SIEMs** and ticket escalation (e.g., Jira, ServiceNow)

### 🧪 ISO 27005 & HIPAA Alignment

- ISO: Use Annex A controls and risk process from 27005  
- HIPAA: Ensure safeguards per Security Rule (access control, audit controls, integrity, transmission security)

### 📊 Metrics for Success

- 📉 Reduction in number of unmitigated high-risk items  
- 📋 Risk register adoption across departments  
- 🛡️ Audit success and certification progress  
- 🔁 Decreased response time to risk-related findings  

---

### 🧠 Real-World Scenario — Security Governance

**Question:**  
Your organization recently faced a security incident where confidential HR data was leaked due to the use of an unapproved SaaS tool. Despite having MFA and firewall rules in place, no explicit policy was violated, and the tool was used by an HR manager with admin-level access.

➡️ *How would you design a governance structure that prevents such incidents in the future, even when no technical policy was breached?*

In your answer, cover:
- 🎯 Governance-level controls  
- 📜 Policy creation or refinement  
- 🧑‍💼 Ownership and accountability assignment  
- 📈 How you’d measure effectiveness of governance

---

**Answer:**  
For designing this governance structure, we first need to understand all of our threats and incidents that led to this breach. The incident occurred due to an HR manager using an unapproved SaaS tool and having excessive admin-level access.  

To prevent such incidents, we must:

- ✅ **Enable formal governance frameworks** like ISO 27001 and NIST RMF that define access controls, asset classification, and compliance mandates.
- ✅ **Define and enforce approved SaaS usage policies**, ensuring that only tools vetted by the security team are allowed within the organization.
- ✅ **Implement Role-Based Access Control (RBAC)** — ensuring that HR personnel only have access appropriate to their job functions and nothing more.
- ✅ **Establish clear accountability**: assign data stewardship and app approval responsibilities to specific roles (e.g., GRC lead, IT security manager).
- ✅ **Track and enforce policy compliance** using automated tooling (e.g., SaaS shadow IT detectors, DLP alerts).
- ✅ **Set up governance metrics** such as:
  - % of approved vs unapproved SaaS usage  
  - Mean time to detect unapproved tools  
  - Role-policy alignment score  
  - Compliance score against frameworks like ISO 27001

This governance structure creates proactive, policy-aligned oversight and ensures no individual can act beyond their defined role without triggering visibility and review.

---

# 📄 File: quiz.md

---

## 🛠️ Security Controls (Preventive, Detective, Corrective)

**🧨 Real-World Scenario:**  
A fintech company suffered a silent breach due to an outdated third-party library. Sensitive customer PII and payment data was exfiltrated over several days without detection.

**🎯 Question:**  
As a security engineer, how would you design a control strategy using **Preventive, Detective, and Corrective** controls to avoid or contain such incidents in the future?

---

**✅ Answer:**

- **Preventive Controls:**
  - Enforce patching and upgrading of third-party libraries regularly
  - Integrate Software Composition Analysis (SCA) tools like Snyk, Dependabot, or OWASP Dependency-Check in CI/CD
  - Apply least privilege to prevent wide access in case of compromise

- **Detective Controls:**
  - Deploy IDS tools (Snort, Suricata) to monitor for abnormal behavior
  - Implement a SIEM system (e.g., Splunk, ELK) for log aggregation and alerting
  - Enable audit logs and anomaly-based access alerts

- **Corrective Controls:**
  - Immediately patch known vulnerabilities upon discovery
  - Execute incident response with system isolation and forensic investigation
  - Perform root cause analysis (RCA) and improve control coverage