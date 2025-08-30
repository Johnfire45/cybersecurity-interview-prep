## 3.4.1 Build Process & Gradle

- **Gradle** automates Android build, manages dependencies, and creates APKs.  
- **Build Types:**  
  - `debug`: debuggable, logs enabled.  
  - `release`: optimized, obfuscated, signed.  
- **Build Variants:** Combines build type + product flavor (e.g., `freeDebug`, `paidRelease`).  
- **Risks:**  
  - Shipping debug build to prod.  
  - Hardcoded secrets in gradle files.  
  - Using outdated/insecure dependencies.  
- **Best Practices:**  
  - Always obfuscate & shrink release builds (R8/ProGuard).  
  - Validate dependencies for CVEs.  
  - Keep API keys/secrets out of gradle files.  

  ## 3.4.2 APK Structure & DEX Files

### APK Structure
- APK is a ZIP archive with:  
  - `AndroidManifest.xml` (config & permissions).  
  - `classes.dex` (compiled bytecode).  
  - `resources.arsc`, `res/`, `assets/` (resources & configs).  
  - `lib/` (native libraries).  
  - `META-INF/` (signing certs & hashes).

### DEX Files
- DEX = Dalvik Executable format run by ART.  
- Contains all app logic.  
- Tools like `jadx`, `dex2jar`, `apktool` can reverse to readable code.

### Security Risks
- **Reverse Engineering:** No obfuscation → readable class/method names, business logic exposed.  
- **Hardcoded Secrets:** API keys/tokens often embedded.  
- **Tampering & Repackaging:** Attackers can modify DEX and re-sign APK.  
- **JNI Risks:** Unsafe native calls can lead to memory exploits.

### Attack Tools
- **Static:** `jadx`, `dex2jar`, `apktool`.  
- **Dynamic:** `Frida`, `Objection`, `Xposed`.

### Best Practices
- Enable **ProGuard/R8 obfuscation** for release builds.  
- Keep secrets server-side (not in DEX).  
- Validate code integrity (Play Integrity API/SafetyNet).  
- Scan APKs with **MobSF** before release.  
- Use native obfuscation cautiously for sensitive logic.

---

### Interview One-Liner
“DEX files hold all app logic. Without obfuscation, reverse engineering and tampering are trivial. Protect with R8, avoid hardcoded secrets, and enforce runtime integrity checks.”

## 3.4.3 Debugging & Logging

### Debugging
- `android:debuggable=true` enables runtime debuggers.  
- **Risk in production:** Attackers can attach debuggers, dump memory, bypass checks, and manipulate runtime.  
- Tools: `adb jdwp`, Frida, Objection.  

### Logging
- Developers use `Log.d()`, `Log.i()`, etc.  
- **Risk in production:** API tokens, session cookies, or PII may be leaked in logcat.  
- Attackers can extract logs via `adb logcat` or on rooted devices.  

### Common Mistakes
- Leaving verbose logs in release builds.  
- Printing sensitive tokens/credentials.  
- Forgetting to strip debug symbols in NDK apps.  

### Best Practices
- Always set `android:debuggable=false` in release.  
- Strip debug info with ProGuard/R8.  
- Disable verbose logging in release builds.  
- Use conditional logging (debug-only).  
- Enforce CI/CD checks to block sensitive log statements.  

---

### Interview One-Liner
“Debuggable builds and verbose logs left in production expose sensitive data and runtime controls. Always disable debuggable, strip debug info, and remove sensitive logs in release builds.”

## 3.4.4 Testing & Quality Assurance (QA)

### Security Risks
- **Leftover test code/credentials** → attackers may find staging accounts or hardcoded creds in APK.  
- **Weak input validation** → unchecked user input leads to SQLi, path traversal, etc.  
- **Insecure configs** → exported components, debuggable/logging enabled slip through QA.  
- **Missed regression checks** → security bugs reappear without automated security testing.

### Best Practices
- Add **security test cases** to unit & integration tests.  
- Remove test code, logs, and debug endpoints before release.  
- Use **mock services** instead of hardcoded test creds.  
- Integrate **automated security scans** (MobSF, SonarQube) into CI/CD.  
- Apply **OWASP MASVS** as a QA standard.  
- Conduct **regular pentesting** before release.

---

### Interview One-Liner
“Security-focused QA ensures test creds, weak validation, and insecure configs don’t slip into production. Automated scans, MASVS, and regular pentests are key for secure Android QA.”