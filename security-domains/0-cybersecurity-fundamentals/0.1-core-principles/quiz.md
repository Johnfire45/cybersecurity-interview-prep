
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