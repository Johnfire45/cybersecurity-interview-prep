### 3.6.1 SharedPreferences Security — Quiz Summary (quiz.md)

---

#### Q4 — Subjective (🧨 Real-World Scenario)

**Scenario:**  
An e-commerce app stores the following in `shared_prefs/userData.xml`:  
- `userEmail="alice@example.com"`  
- `authToken="8fj39skdke92..."` (long-term token valid for API calls)  
- `isPrimeUser="false"`  
- `cartDiscount="20%"`  

The manifest has `allowBackup="true"`.  
Dynamic tests show:  
- Flipping `isPrimeUser` unlocks premium-only features.  
- API calls accept `authToken` without expiry/refresh checks.  
- Discounts applied locally before checkout submission.

**Expected Answer:**  

1. **Prioritized Risks:**  
   - Critical: Long-lived `authToken` trusted without server validation → account takeover.  
   - Critical: `allowBackup=true` enables token/flag extraction via adb backup.  
   - High: `isPrimeUser` flag → local privilege escalation to premium features.  
   - High: `cartDiscount` → client-side price manipulation.  
   - Medium: `userEmail` stored in plaintext → PII exposure.  

2. **Attack Scenarios:**  
   - Token theft → replay in API requests, full impersonation.  
   - Flip `isPrimeUser` → gain premium access without entitlement.  
   - Tamper `cartDiscount` → fraud by lowering checkout price.  
   - Combine → attacker extracts token + modifies prefs for complete compromise.  

3. **Mitigations:**  
   - Set `allowBackup="false"` or restrict backups via `fullBackupContent`.  
   - Replace long-lived tokens with short-lived, server-validated tokens.  
   - Validate premium/discounts strictly server-side — client flags only for UI.  
   - If prefs are needed for UX, use EncryptedSharedPreferences with Keystore, but not for authority.  
   - Minimize PII storage; avoid keeping `userEmail` locally.  

**OWASP Mapping:**  
- A01 — Broken Access Control  
- A02 — Sensitive Data Exposure  
- A05 — Security Misconfiguration  
- Business Logic Exploit (price manipulation)  

**Interview One-Liner:**  
“AllowBackup plus plaintext SharedPreferences exposes tokens and flags; attackers can hijack sessions, unlock premium, and tamper discounts — server-side validation and disabling backup are critical.”

### 3.6.4 SQLite Database Security — Quiz Summary (quiz.md)

---

#### Q4 — Subjective (🧨 Real-World Scenario)

**Scenario:**  
A payments app exposes a ContentProvider `com.payments.provider` (exported, no `android:permission`) that maps to `payments.db`.  
- `query()` builds SQL by concatenating `selection` and `sortOrder` into `rawQuery()` with no parameterization.  
- `payments.db` stores `transactionId`, `amount`, `recipientAccount`, and `status`.  
- Manifest has `allowBackup=true`.  
- Business logic: if a row with `status="PENDING"` exists, the app allows local “quick confirm” with no server re-validation.

**Expected Answer:**  

1. **Primary Risks (prioritized):**  
   - SQL Injection in exported provider → full DB dump or tampering (Critical).  
   - `allowBackup=true` → adb backup exfiltration of DB (Critical / enabler).  
   - Trusting local `PENDING` flag → client-side logic bypass for financial transactions (High).  
   - Residual sensitive state (DB persists after logout) (High).  
   - Exported provider with no permission → broad attack surface (High).  

2. **Realistic Exploit Chains:**  
   - **Chain A (Exfiltration):** Use adb backup (allowBackup) → dump `payments.db` → extract tokens/transactions → impersonate users.  
   - **Chain B (Logic bypass):** Craft injection via provider `selection` → force `PENDING` row → app quick-confirm flow executes unauthorized transaction since no server-side check.  

3. **Mitigations (urgent + process):**  
   - Immediate: disable exports or require signature-level permission; refactor queries to use `selectionArgs`; disable `allowBackup`.  
   - Short term: enforce server-side validation of confirmations; encrypt DB with SQLCipher/Keystore.  
   - Medium term: add CI gates for insecure manifest flags and rawQuery concatenation; require security review for exported providers.  
   - Defense-in-depth: purge old records, vacuum DB, enforce step-up auth for confirmations, monitor abnormal provider calls.  

**OWASP Mapping:**  
- A03 — Injection  
- A01 — Broken Access Control  
- A02 — Sensitive Data Exposure  
- A05 — Security Misconfiguration  

**Interview One-Liner:**  
“Exported ContentProvider + rawQuery injection + allowBackup + client-trusted PENDING flags form a full-chain compromise — fix injection, disable backups, and enforce server-side validation.”