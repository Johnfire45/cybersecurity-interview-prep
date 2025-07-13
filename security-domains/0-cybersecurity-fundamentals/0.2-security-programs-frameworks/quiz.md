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

