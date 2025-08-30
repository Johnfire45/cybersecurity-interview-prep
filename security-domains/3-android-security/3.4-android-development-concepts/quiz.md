### Q2 — MCQ: Gradle Release Config Risks

**Question:**  
During a pentest, you decompile a production APK and see this in the `build.gradle`:

    buildTypes {
        release {
            minifyEnabled false
            shrinkResources false
            signingConfig signingConfigs.release
        }
    }

Which two risks stand out the most?

---

**Answer:**  
- **A) APK size will increase due to unused code/resources**  
- **B) Sensitive methods and class names remain exposed for reverse-engineering**  

---

**Explanation:**  
- `minifyEnabled false` → no ProGuard/R8 → no obfuscation → class/method names are human-readable → easier reverse-engineering and patching.  
- `shrinkResources false` → unused resources included → larger APK, may leak unused sensitive strings/endpoints.  

### Q7 — Subjective: Fintech App APK Risks

**Question:**  
During a pentest of a fintech app, you discover:  
- `classes.dex` reveals a `PaymentValidator` class with clear business logic (no obfuscation).  
- Hardcoded API keys for third-party payment gateways are visible in decompiled code.  
- The APK is signed with v2 but has no runtime integrity checks.  
- There are no protections against repackaging (no SafetyNet/Play Integrity, no tamper detection).  

Explain the combined risks of these findings and what defenses should be recommended.

---

**Answer (Summary):**

1. **PaymentValidator Logic Exposed**  
- Risk: Reverse engineering exposes validation logic; attackers may bypass client-side checks.  
- Defense: Shift sensitive validation server-side, enable R8/ProGuard obfuscation.

2. **Hardcoded API Keys**  
- Risk: Keys extracted from DEX → attackers can abuse APIs, impersonate the app, or trigger fraudulent transactions.  
- Defense: Never hardcode keys; use secure vaults or backend-issued ephemeral tokens, rotate keys if leaked.

3. **No Runtime Integrity Checks**  
- Risk: APK can be modified, re-signed with a new key, and distributed as a malicious clone.  
- Defense: Implement Play Integrity API/SafetyNet, checksum validation, and tamper detection to detect re-signed apps.

---

**Interview One-Liner:**  
“Exposed validation logic, hardcoded keys, and no runtime integrity checks allow attackers to reverse engineer, bypass protections, and repackage malicious clones. Protect with server-side validation, secured key handling, obfuscation, and runtime integrity enforcement.”

### Q3 — Subjective: Debuggable, Verbose Logging & Native Symbols

**Question:**  
During a pentest of a ride-sharing app, you discover:  
- The APK has `android:debuggable="true"` set in the Manifest.  
- Verbose logging is enabled in production and exposes **JWT tokens** and **driver location data** in logcat.  
- The app also ships with unstripped native debug symbols (`.so` libraries contain function names and debug info).  

Explain the combined risks of these findings and what defensive measures should be recommended.

---

**Answer (Summary):**

1. **Debuggable Manifest Flag**  
- Risk: Attackers can attach JDWP debuggers or Frida/Objection to dump memory, bypass validations, and tamper with runtime.  
- Fix: Ensure `android:debuggable="false"` in release builds.

2. **Verbose Logging in Production**  
- Risk: Sensitive tokens and PII logged in logcat can be extracted on rooted devices or via adb. Tokens can be reused for session hijacking or fraud.  
- Fix: Strip sensitive logs in production, use conditional logging (`BuildConfig.DEBUG`), and never log secrets or PII.

3. **Unstripped Native Debug Symbols**  
- Risk: Exposed function names and debug info in `.so` files make reverse engineering trivial and help attackers find memory vulnerabilities.  
- Fix: Strip debug symbols before shipping (`ndk-build NDK_DEBUG=0`) and use native obfuscation if needed.

4. **Combined Risk**  
- Attackers gain runtime control, sensitive tokens, and reverse-engineering shortcuts all at once, drastically lowering the barrier to exploitation.

---

**Interview One-Liner:**  
“Debuggable builds, verbose logs, and unstripped symbols expose runtime control, sensitive data, and reverse-engineering shortcuts. Disable debuggable, strip logs, and remove debug symbols in production.”