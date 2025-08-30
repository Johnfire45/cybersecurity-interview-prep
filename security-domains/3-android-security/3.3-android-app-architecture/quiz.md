### Q6 — Subjective: Misconfigured Manifest in Fintech App

**Question:**  
You are pentesting a fintech app and discover:

    <application
        android:allowBackup="true"
        android:debuggable="true">
    </application>

    <activity
        android:name=".TransactionHistoryActivity"
        android:exported="true">
    </activity>

Explain the security risks of these misconfigurations and recommend how the developers should fix them.

---

**Answer:**

1. **allowBackup="true"**  
- Risk: With this flag enabled, any attacker with physical ADB access can dump the app’s private data, including SQLite databases, SharedPreferences, tokens, and sensitive financial information — even on a non-rooted device.  
- Fix: Set `android:allowBackup="false"` in production apps to block ADB backups.

2. **debuggable="true"**  
- Risk: Attackers can attach a debugger to the running app, dump runtime memory, bypass controls, alter execution flow, or reverse-engineer critical logic.  
- Fix: Always set `android:debuggable="false"` in production builds. Keep it enabled only in development builds.

3. **exported Activity (TransactionHistoryActivity)**  
- Risk: Because `exported="true"`, any external app can explicitly launch this sensitive Activity without user consent. This could expose transaction data, bypass authentication flows, or allow malicious manipulation of app behavior.  
- Fix: Set `android:exported="false"` for internal-only activities. If external access is required, enforce a strong `android:permission` with `signature` protection.

---

**Interview One-Liner:**  
“This Manifest misconfiguration allows ADB data dumps, debugger attacks, and external access to sensitive activities. Fix by disabling backups/debugging in production and locking down exported components with `false` or strong permissions.”

### Q6 — Subjective: Key Compromise & Migration

**Question:**  
You are doing a security review of a popular healthcare app.  
- It has millions of installs.  
- The app is signed with **v2 signing scheme** (whole-APK verified).  
- But last week, the **release signing key was compromised**.  
- The dev team wants to know if they can just revoke the old key and generate a new one for the Play Store.  

👉 In an interview, explain the **risks, technical challenges, and the correct migration path**.

---

**Answer:**

1. **Risks of a Compromised Key**
   - Attackers can sign malicious updates with the stolen key → Android trusts them as legitimate updates.  
   - Users won’t see warnings → catastrophic for a healthcare app (PII theft, fraud, large-scale compromise).  

2. **Technical Challenge**
   - Android requires **the same signing key** for all updates.  
   - If you generate a new key → updates for existing users will be rejected.  
   - Without Play App Signing → must publish a new app with a new package name → breaks continuity + user trust.  

3. **Correct Migration Path**
   - **If using Play App Signing:**  
     - Google stores the app signing key.  
     - If the **upload key** is compromised → rotate it easily via Google.  
     - If the **signing key** itself is compromised → since Android 9 (APK Sig v3), Google can support **key rotation**.  
   - **If not using Play App Signing:**  
     - No key rotation possible.  
     - Only option = release a new app with a new package name → very costly.  

4. **Defensive Actions**
   - Immediately revoke compromised key from internal systems.  
   - Contact Google Play Security → request key upgrade (if v3+ supported).  
   - Warn users against side-loaded updates.  
   - Long-term: use **Play App Signing** + secure keys in **HSM/keystores**, never repos.  

---

**Interview One-Liner:**  
“A compromised release key means attackers can ship malware that looks like trusted updates. With Play App Signing and v3, request Google for key rotation. Without it, continuity breaks, proving why secure storage and Play App Signing are essential.”  

### Q1 — Subjective: Staging Endpoints in Release APK

**Question:**  
During a pentest, you decompile a financial app and notice:  
- The Play Store release APK has `android:debuggable="false"`.  
- But inside the code, staging endpoints and API keys from a debug flavor are still bundled.  
- They are not used in production paths but are present in resources.  

Explain the security risks of this misconfiguration and what remediation steps developers should take.

---

**Answer (Summary):**

1. **Security Risks**  
- Exposed staging endpoints/API keys can be extracted and abused.  
- Staging systems often have weaker defenses → used as a pivot.  
- If staging credentials overlap with production → attacker escalates to prod.  
- Even unused data gives attackers insight into internal systems and practices.  

2. **Remediation**  
- Exclude debug/test flavors from release builds in `build.gradle`.  
- Never bundle secrets in APK; use secure vaults or runtime config.  
- Validate CI/CD pipelines to ensure release APKs contain only production resources.  
- Run static analysis to detect leftover secrets.  

---

**Interview One-Liner:**  
“Even with debuggable=false, leaving staging endpoints and API keys in release APKs exposes sensitive infra and risks credential reuse. Fix by cleaning flavor configs and removing secrets from APKs via secure CI/CD.”  