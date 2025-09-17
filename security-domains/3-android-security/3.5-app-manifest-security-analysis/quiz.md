### Q4 — Subjective: Permission Analysis Wrap-Up

**Question:**  
You’re pentesting a finance app and discover in its `AndroidManifest.xml`:

    <uses-permission android:name="android.permission.READ_SMS" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <uses-permission android:name="android.permission.INTERNET" />

    <permission android:name="com.financeapp.SECURE_ACCESS"
        android:protectionLevel="signature" />

Findings:  
1. The app requests `READ_SMS`, `SYSTEM_ALERT_WINDOW`, and `INTERNET`.  
2. It defines a custom `signature` permission but doesn’t use it to protect any component.  

---

**Answer (Summary):**

1. **Custom `signature` Permission Misconfigured**  
- Risk: Permission is defined but unused → no component is protected, creating a false sense of security.  
- Fix: Apply it to sensitive components (e.g., Activities, Services).  

2. **Dangerous Permissions**  
- `READ_SMS`: High risk of OTP theft and account takeover.  
- `SYSTEM_ALERT_WINDOW`: Risk of phishing overlays / tapjacking attacks.  
- `INTERNET`: Normal, but enables data exfiltration when combined with above.  

3. **Combined Risk**  
- Over-privileged + unused signature protection.  
- OTP theft + malicious overlays → severe account compromise.  

4. **Recommendations**  
- Remove `READ_SMS` → use SMS Retriever API.  
- Restrict `SYSTEM_ALERT_WINDOW` → only if essential, add controls.  
- Enforce **principle of least privilege**.  
- Apply signature permission to sensitive components.  
- Audit permissions before release.  

---

**Interview One-Liner**  
“Excessive dangerous permissions (`READ_SMS`, `SYSTEM_ALERT_WINDOW`) create risks of OTP theft and phishing overlays. Meanwhile, the unused signature permission wastes a security control. Fix = least privilege, secure OTP APIs, and enforce signature permissions.”

### Q1 — MCQ (Real-World Scenario)

**Scenario:**  
`PayActivity` is declared with `android:exported="true"`. It completes payments if it receives an `orderToken` extra, validated locally, with no `android:permission` protection.

**Correct Answer:**  
B — Activity Hijacking / Forged Intent (Auth Bypass)

**Reasoning:**  
- Exported Activity + client-supplied token = direct authorization bypass.  
- Any app can craft an intent with a fake `orderToken` and trigger payment.  
- No manifest-level permission or runtime caller validation in place.  
- Other options (SQL injection, service binding, broadcast spoofing) are not directly relevant.

**Exploitation Concept (pentester framing):**  
- Attacker app can invoke `PayActivity` and supply crafted extras.  
- The app trusts the intent and completes sensitive actions (e.g., payment) without authorization.  
- This is a classic Activity Hijacking scenario, often one of the first things pentesters check when auditing manifests.

**Mitigations:**  
1. Set `android:exported="false"` unless absolutely required.  
2. If external access is needed, add a strong `android:permission` (prefer `signature`).  
3. Perform server-side token validation — never rely only on client-side checks.  
4. Validate caller identity (`Binder.getCallingUid()`, package signature checks).  
5. Use one-time, short-lived server-issued tokens tied to a session.  
6. Add explicit user confirmation for payment actions.

**OWASP Mapping:**  
- A01 — Broken Access Control  
- A02 — Sensitive Data Exposure / Auth Bypass  

**Interview One-Liner:**  
“An exported Activity that auto-trusts extras for sensitive actions is an immediate auth bypass risk.”

**Scenario:**  
An exported ContentProvider has:  
- `android:exported="true"`  
- No `android:permission`  
- SQL queries built with string concatenation:  
  `SELECT * FROM files WHERE owner = '" + userId + "';`

**Expected Answer:**  
- Risks:  
  1. Unauthorized access — any app can query it.  
  2. SQL Injection — concatenation lets attacker alter query logic.  
  3. Sensitive Data Exposure — attacker can exfiltrate or tamper with stored data.  

- Mitigations:  
  - Restrict exposure (`android:exported="false"` unless required).  
  - If external sharing needed, enforce `android:permission` (prefer `signature`).  
  - Use parameterized queries (`selectionArgs`, `SQLiteQueryBuilder`) instead of string concatenation.  
  - Apply `pathPermissions` or narrow `grantUriPermissions` where sharing is necessary.  
  - Validate caller identity (`Binder.getCallingUid()` → package allowlist).  

**OWASP Mapping:**  
- A01 — Broken Access Control  
- A03 — Injection  
- A02 — Sensitive Data Exposure  

**Interview One-Liner:**  
“An exported provider without permissions + raw SQL is a direct pipeline for injection and data theft.”

### 3.5.3 Intent Filter Security — Quiz Summary (quiz.md)

---

#### Q1 — MCQ (🧨 Real-World Scenario)

**Scenario:**  
A ride-sharing app declares an intent filter with a custom scheme:

    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
        <data android:scheme="riderapp" android:host="payment" />
    </intent-filter>

It is used for handling payment callbacks.

**Correct Answer:**  
A — Any malicious app can register the same custom scheme (`riderapp://`) and steal payment callbacks.

**Reasoning:**  
- Custom schemes are not exclusive — multiple apps can claim the same scheme.  
- A malicious app could intercept sensitive data like tokens.  
- Uniqueness of the scheme name offers no security.  
- App Link domain verification does not apply to custom schemes.

**Mitigations:**  
- Use Android App Links (`autoVerify="true"`) with HTTPS and Digital Asset Links.  
- Avoid transmitting secrets via custom schemes.  
- Require server-side validation of tokens and enforce short-lived, single-use values.  
- Detect unexpected handler apps and notify the user.

**OWASP Mapping:**  
- A01 — Broken Access Control  
- A02 — Sensitive Data Exposure  

**Interview One-Liner:**  
“Custom schemes are insecure — any app can hijack them; verified HTTPS App Links are the fix.”

### 3.5.3 Intent Filter Security — Quiz Summary (quiz.md)

---

#### Q2 — MCQ (🧨 Real-World Scenario)

**Scenario:**  
A fitness app declares this intent filter for handling OAuth login callbacks:

    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
        <data android:scheme="https" android:host="fitapp.com" android:path="/oauth/callback"/>
    </intent-filter>

The app does **not** use `android:autoVerify="true"`, and the domain `https://fitapp.com` does not host a Digital Asset Links file.

**Correct Answer:**  
B — Any app can register the same host/path and intercept OAuth tokens.

**Reasoning:**  
- HTTPS scheme alone does not guarantee exclusivity.  
- Without App Link verification (`autoVerify` + `assetlinks.json`), Android cannot prove the app is the legitimate owner of the domain.  
- Another app can register the same host/path and hijack the OAuth callback intent.  

**Mitigations:**  
- Use Android App Links with `android:autoVerify="true"`.  
- Publish a valid `assetlinks.json` on the domain for verification.  
- Ensure OAuth tokens/codes are short-lived, single-use, and validated server-side (with PKCE).  
- Avoid relying on unverified deep links for sensitive operations.  

**OWASP Mapping:**  
- A01 — Broken Access Control  
- A02 — Sensitive Data Exposure  

**Interview One-Liner:**  
“HTTPS deep links without App Link verification can be hijacked — App Links + Digital Asset Links are mandatory for secure OAuth callbacks.”

### 3.5.4 Manifest Hardening — Quiz Summary (quiz.md)

---

#### Q2 — Subjective (🧨 Real-World Scenario)

**Scenario:**  
A financial services app’s `AndroidManifest.xml` shows multiple misconfigurations:  
- Several Activities and Services marked `android:exported="true"` with no `android:permission`.  
- `android:allowBackup="true"` enabled.  
- `android:debuggable="true"` present.  
- `android:usesCleartextTraffic="true"`.  
- `LoginActivity` has a custom `taskAffinity`.  

**Expected Answer:**  

1. **Prioritization (by severity):**  
   - **Debuggable=true** → highest severity; attackers can attach debugger and extract secrets in production.  
   - **Exported components w/o permission** → auth bypass, privilege escalation, direct abuse.  
   - **AllowBackup=true** → data exfiltration via adb backup.  
   - **Cleartext traffic** → MITM, session hijacking.  
   - **Custom taskAffinity** → task hijacking, phishing vector.  

2. **Attack Scenarios:**  
   - Debuggable build: runtime memory dump of tokens/keys.  
   - Exported components: external apps trigger privileged flows (e.g., payments, DB queries).  
   - Backup enabled: attacker pulls local SQLite DB containing PII/tokens.  
   - Cleartext traffic: credentials intercepted over Wi-Fi.  
   - TaskAffinity: attacker app injects fake login page into task stack → credential theft.  

3. **Mitigations:**  
   - Disable `debuggable` in release builds.  
   - Default `exported=false`; require `signature`-level permission for necessary IPC.  
   - Set `allowBackup=false` or restrict backups with `fullBackupContent`.  
   - Disable cleartext traffic; enforce TLS and use networkSecurityConfig with pinning.  
   - For sensitive activities: set `taskAffinity=""`, `excludeFromRecents=true`, and `singleTask` launch mode.  

**OWASP Mapping:**  
- A01 — Broken Access Control (exported comps, task hijacking)  
- A02 — Sensitive Data Exposure (allowBackup, cleartext traffic)  
- A03 — Injection (if unsafe SQL in providers)  
- A05 — Security Misconfiguration (debuggable, manifest flags)  

**Interview One-Liner:**  
“In a financial app, misconfigs like debuggable, exported comps, backup enabled, cleartext, and taskAffinity chaining create a full-chain compromise — fix manifest hygiene first.”