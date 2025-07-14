# 📄 File: notes.md  
## 🔐 Risk Management — Summary Notes

### ✅ What Is Risk Management?
A formal process used to identify, assess, prioritize, and treat risks to organizational assets (people, data, infrastructure). Ensures risks are addressed proactively, not reactively.

---

### 🧱 Core Risk Management Frameworks

| Framework | Focus | Key Points | Best For |
|----------|-------|------------|----------|
| **NIST RMF** | U.S. Federal Systems | 6-step lifecycle: Categorize → Select → Implement → Assess → Authorize → Monitor | Compliance-heavy orgs |
| **ISO 31000** | Enterprise Risk | Strategic, business-aligned risk mgmt | Global orgs, business integration |
| **FAIR** | Quantitative Risk | Measures cyber risk in $$ | GRC, CISO-level reporting |
| **OCTAVE** | Operational Risk | Asset/threat-centric, business process driven | Mid-sized orgs |
| **COBIT** | Governance & Audit | IT control/governance, compliance | Auditors, GRC frameworks |

---

### 🔁 Typical RMF Workflow

1. **Asset Inventory** – Identify what to protect
2. **Threat Modeling / Risk Identification**
3. **Vulnerability Assessment**
4. **Risk Evaluation** – Likelihood × Impact
5. **Control Implementation** – Preventive/Detective/Corrective
6. **Monitoring** – Ongoing risk validation and control tuning

---

### 🧠 Interview Insights

| Asked As... | You Should Discuss... |
|-------------|------------------------|
| “How do you choose a risk framework?” | Depends on regulatory, business, or quant needs |
| “How to report risk to execs?” | Use FAIR to translate technical risk into financial exposure |
| “What’s the difference between ISO 31000 and NIST RMF?” | ISO = business risk, NIST = IT/system lifecycle |
| “How to audit controls?” | Mention COBIT, control objectives, policy enforcement |

---

### 🛠️ Common Tools & Practices
- **GRC Platforms**: ServiceNow, Archer
- **Risk Registers**: Excel or tooling-based tracking of risk entries
- **Control Frameworks**: NIST 800-53, CIS Controls
- **Metrics**: Risk heatmaps, residual risk scores, KRIs (Key Risk Indicators)

---

### 📌 Key Takeaways
- RMFs are essential in **governance**, **compliance**, and **long-term security posture**.
- **Choosing the right RMF** depends on org size, industry, regulatory needs, and leadership goals.
- Combine **RMF + threat modeling** for both top-down and system-level assurance.

## 🔐 0.2.2 Security Governance — Summary Notes

### ✅ What is Security Governance?
Security Governance is the strategic layer of cybersecurity, focusing on aligning security with business objectives, accountability, and regulatory compliance. It is concerned with **who is responsible**, **what policies guide decisions**, and **how outcomes are measured**.

---

### 🧩 Core Components
- **Governance Frameworks**: ISO 27001, COBIT, NIST CSF  
- **Policies**: Acceptable Use, Data Classification, Access Control  
- **Roles & Accountability**: CISO, CIO, Security Committees  
- **Risk Appetite**: Defines acceptable risk thresholds  
- **Metrics**: Incident count, response time, compliance status  
- **Security Committees**: Oversight boards for security direction  
- **Budget Alignment**: Security investment linked to risk areas

---

### 📊 Governance vs Management vs Operations
| Governance | Management | Operations |
|------------|------------|------------|
| Strategic direction | Tactical execution | Technical control |
| Policy setting | Enforcement planning | Daily monitoring |

---

### 💡 Interview-Relevant Highlights
- Governance ensures security is **business-aligned**
- Clear RACI chart (who owns, approves, consults, informed)
- Success measured via KPIs and audits
- Often tied to standards like ISO 27001 or NIST CSF

---

## 🔐 Security Controls – Preventive, Detective, Corrective

### ✅ What Are Security Controls?
Security controls are technical, administrative, or physical measures to mitigate, detect, or correct risks to systems.

---

### 🧩 Control Categories

| Category      | Purpose                                   | Examples                                                   |
|---------------|-------------------------------------------|-------------------------------------------------------------|
| Preventive    | Block incidents before they occur         | MFA, firewalls, access control, security policies           |
| Detective     | Discover incidents during or after        | SIEM, audit logs, IDS/IPS                                   |
| Corrective    | Respond to incidents and restore systems  | Patch management, backups, terminating compromised accounts |

---

### 🧱 Control Layers

| Layer          | Preventive              | Detective                 | Corrective              |
|----------------|--------------------------|---------------------------|--------------------------|
| Administrative | Onboarding rules         | HR audit logs             | Disciplinary policies    |
| Technical      | MFA, RBAC                | IDS, SIEM, logs           | Patch, revoke access     |
| Physical       | Badges, security gates   | CCTV                      | Lock replacement         |

---

### 🛠️ Real-World Use
A robust system implements all three types for layered defense:
- **Preventive**: RBAC + MFA
- **Detective**: SIEM + alerts
- **Corrective**: Account suspension + forensics

---

### 🎯 Interview Insights
- Focus on mapping each control to its type and effectiveness
- Be ready to design control sets for:
  - Data breaches
  - Insider threats
  - Compliance failures

--

## 🛡️ NIST / ISO / CIS Controls Overview — Summary Notes

### 🔍 What are Security Controls?

Security controls are specific safeguards or countermeasures implemented to:
- Prevent security risks  
- Detect ongoing threats  
- Correct after incidents  

They provide a structured defense mechanism across an organization’s people, processes, and technology.

---

### 🏛️ Key Control Frameworks

| **Framework** | **Purpose** | **Key Insight** |
|---------------|-------------|----------------|
| **NIST 800-53 / NIST CSF** | Comprehensive U.S. federal security control catalog | Covers technical, administrative, and physical controls; highly detailed |
| **ISO/IEC 27001** | International standard for Information Security Management Systems (ISMS) | Focuses on risk-based management and continuous improvement |
| **CIS Controls** | Practical, prioritized set of defensive actions | Focused on real-world threats; provides a minimum baseline |

---

### 🧰 Control Categories (PDC Model)

| Type | Description | Examples |
|------|-------------|----------|
| **Preventive** | Stop incidents before they happen | RBAC, MFA, Network Segmentation |
| **Detective** | Identify threats when they occur | SIEM, IDS/IPS, Logging |
| **Corrective** | Lessen damage, restore systems | Patch Management, Incident Response, Backups |

---

### ⚙️ How They Work Together

- **Preventive Controls**: Set up walls to block attacks  
- **Detective Controls**: Act as security cameras to alert on suspicious behavior  
- **Corrective Controls**: Serve as recovery teams after incidents  

---

### 🎯 Interview Angle

- Be ready to compare NIST, ISO, and CIS based on **context**:
  - NIST → U.S. Federal, detailed compliance  
  - ISO → Global, risk-based and strategic  
  - CIS → Tactical, immediate controls for SMBs and enterprise alike

- Map controls to **real security incidents** in your experience.

--

## 🔐 Security Policies & Compliance (SOC2, HIPAA, etc.)

### ✅ What are Security Policies?
- Formalized rules and guidelines that define how an organization secures data, infrastructure, and operations.
- Align technical and business processes with security best practices.

### 🧰 Compliance Frameworks Overview:

| Framework | Purpose | Focus |
|-----------|---------|-------|
| **SOC 2** | Service Organization Controls for Security, Availability, Processing Integrity, Confidentiality, Privacy | Primarily for SaaS and cloud providers |
| **HIPAA** | Health Insurance Portability and Accountability Act | Protects health-related PII (PHI/PII compliance) |
| **PCI DSS** | Payment Card Industry Data Security Standard | Secure credit card processing and storage |
| **ISO 27001** | International Standard for Information Security Management | Comprehensive ISMS (Information Security Management System) |
| **GDPR / CCPA** | Data Privacy Laws | Protect personal data, user consent, data portability |

### 🔑 Core Components of Security Policies:

- **Access Control Policies:** Define who can access what.
- **Data Classification:** Label data based on sensitivity (PII, PHI, PCI).
- **Incident Response Plans:** Define how to handle breaches or incidents.
- **Acceptable Use Policies:** Guide how employees can use company assets.
- **Vendor Management:** Ensure third parties comply with security standards.

### 📊 Why Are Policies & Compliance Critical?

- Prevent legal liability & fines  
- Establish a culture of security  
- Protect customer trust  
- Ensure repeatable security practices  
- Meet audit & regulatory requirements  

### ⚠️ Common Interview Points:

- How does SOC2 differ from ISO 27001?
- When is HIPAA applicable vs GDPR?
- Role of technical controls in policy enforcement (e.g., RBAC, MFA, Logging)