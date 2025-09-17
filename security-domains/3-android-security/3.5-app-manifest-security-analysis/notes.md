## 3.5.1 Permission Analysis

### Permission Levels
- **Normal:** Low-risk, auto-granted at install.  
  - Example: `INTERNET`, `SET_WALLPAPER`.  
- **Dangerous:** High-risk, access to sensitive user data/functions.  
  - Example: `READ_CONTACTS`, `READ_SMS`, `RECORD_AUDIO`.  
  - Require runtime user approval (Android 6.0+).  
- **Signature:** Only granted if requesting app signed with same cert as defining app.  
  - Example: Trusted inter-app communication.  
- **SignatureOrSystem:** (Legacy) Granted to system apps or platform-signed apps.  
  - Example: System-level telephony controls.  
- **Custom:** Developers define new permissions with chosen protection level.

### Risks
- Over-privileged apps request unnecessary permissions.  
- Dangerous permissions abused for data exfiltration.  
- Signature perms compromised if signing key is stolen.  
- Custom permissions misconfigured → external app abuse.

### Best Practices
- Follow **least privilege principle**.  
- Use **runtime permissions** for sensitive requests.  
- Audit permission usage regularly.  
- Justify all dangerous/signature permissions in security reviews.  

---

### Interview One-Liner
“Android defines Normal, Dangerous, Signature, and legacy SignatureOrSystem permissions. Misuse or over-requesting them creates huge attack surfaces — least privilege and runtime permissions are critical.”

## 3.5.2 Component Security — Summarized Notes (notes.md)

- ✅ **Scope & Principle:**  
    - Treat every manifest-declared component (Activities, Services, BroadcastReceivers, ContentProviders) as a public API unless explicitly private. Assume adversarial callers for any exported component.

- ⚠️ **Key Manifest Controls:**  
    - `android:exported` — explicit public exposure (`true` = accessible externally). Android 12+: components with `intent-filter` **must** explicitly declare `android:exported`.  
    - `intent-filter` — makes component discoverable via implicit intents (higher hijack risk).  
    - `android:permission` — enforces caller to hold a permission; protection level matters (`signature` > `dangerous`/`normal`).  
    - ContentProvider-specific: `authorities`, `grantUriPermissions`, `pathPermissions`.  
    - `android:enabled` can disable a component even if exported.

- ❌ **High-Impact Misconfigurations:**  
    - Exporting UI or data-handling components without permissions (Activity/Provider/Service).  
    - ContentProviders using string-concatenated SQL queries (SQL injection / data exfiltration).  
    - Bound services (AIDL/Binder) that don’t validate `Binder.getCallingUid()` or call `checkCallingOrSelfPermission()`.  
    - BroadcastReceivers accepting sensitive system-like intents with no permission requirement.

- 🧩 **Component-specific Risks & Notes:**  
    - **Activities:** extras = input vectors; risk = auth bypass, OAuth callback interception, task-affinity exploits. Use explicit intents for sensitive flows; validate extras server-side when possible.  
    - **Services:** bound services expose RPC surface; require runtime caller checks (`getCallingUid()` → `getPackagesForUid()` mapping) and manifest permission.  
    - **BroadcastReceivers:** static receivers can be triggered while app is not running; require permission or use explicit component names for critical flows. Prefer in-app dynamic broadcasts for internal messaging.  
    - **ContentProviders:** biggest data-exfil risk. Use parameterized queries, `SQLiteQueryBuilder`, `selectionArgs`, `pathPermissions`. Prefer `FileProvider` + scoped access for files instead of raw DB exposure.

- 🛡️ **Mitigations & Hardening Checklist:**  
    - ✅ Default `android:exported="false"`; explicitly open only minimal contract.  
    - ✅ Explicitly set `android:exported` for all components (Android 12+ compliance).  
    - ✅ Use `android:permission` with appropriate protection level; consider `signature` for same-signer APIs.  
    - ✅ Runtime checks: `Binder.getCallingUid()` validation, `checkCallingOrSelfPermission()`, map UID->package allowlist for sensitive endpoints.  
    - ✅ Parameterize DB queries; restrict provider URIs with `pathPermissions`; narrow `grantUriPermissions` scope/time.  
    - ✅ Prefer explicit intents for sensitive operations and validate `getPackage()` or package signature where applicable.  
    - ✅ Log/monitor unexpected external binds/starts to detect abuse.

- 🔗 **OWASP Mapping (for reporting):**  
    - Broken Access Control → OWASP A01 (exported components w/o checks).  
    - Injection → OWASP A03 (SQLi in ContentProviders).  
    - Sensitive Data Exposure → OWASP A02 (providers leaking PII/tokens).

- 🧪 **Pentester Detection Checklist (non-actionable):**  
    - Static: inspect `AndroidManifest.xml` for exported components, intent-filters, missing `android:permission`, provider `authorities`.  
    - Dynamic: observe binder binds, provider responses to safe queries, behaviors when receiving external intents (non-destructive).  
    - Prioritize components handling auth/payment/PII for deeper review.

- 💬 **Interview One-Liner:**  
    - “Every exported component is a public API — authenticate, authorize, validate.”

    ## 3.5.3 Intent Filter Security — Summarized Notes (notes.md)

- ✅ **Purpose:**  
    - Intent-filters define how components handle implicit intents.  
    - They control discoverability of Activities, Services, BroadcastReceivers, and deep links.  
    - Improper use leads to hijacking, interception, or abuse of sensitive flows.

- ⚠️ **Implicit vs Explicit Intents:**  
    - Explicit intents → target specific component (safer, less discoverable).  
    - Implicit intents → resolved via system matching; attackers can register matching filters to intercept.

- ❌ **Common Risks:**  
    - **Intent Hijacking:** Malicious apps claim the same scheme/host and intercept sensitive data.  
    - **Deep Link Hijacking:** Custom URI schemes or unverified HTTP/HTTPS links can be stolen by another app.  
    - **OAuth Callback Theft:** Tokens/code sent via deep link intercepted by unauthorized app.  
    - **Parameter Tampering:** Blindly trusting extras or query params from deep links leads to auth bypass or logic flaws.  
    - **Broadcast Injection:** Unprotected intent-filters for receivers enable spoofed broadcasts.

- 🧩 **Deep Links Breakdown:**  
    - **Custom URI Schemes (e.g., myapp://):** Easy to hijack; any app can register same scheme.  
    - **HTTP/HTTPS Deep Links (non-verified):** Any app can claim same domain.  
    - **Android App Links (autoVerify):** Safer; OS verifies ownership via Digital Asset Links (`assetlinks.json`). Prevents hijacking.

- 🛡️ **Mitigations & Best Practices:**  
    - Prefer explicit intents for sensitive actions.  
    - Use **App Links with autoVerify** and set up domain verification.  
    - Avoid placing secrets in deep link parameters; if used, make them single-use and server-validated.  
    - Validate all extras/parameters (schema, type, range).  
    - For OAuth: use Authorization Code Flow with PKCE, not implicit flows.  
    - Require permissions (`android:permission`) for sensitive intent handlers.  
    - For broadcasts: avoid ordered broadcasts for sensitive actions; require permission or use local in-process broadcasts.

- 🔗 **OWASP Mapping:**  
    - A01 — Broken Access Control (unrestricted implicit intents).  
    - A02 — Sensitive Data Exposure (tokens in deep links).  
    - A05 — Security Misconfiguration (missing autoVerify, weak intent handling).

- 💬 **Interview One-Liners:**  
    - “Implicit intents are discovery vectors — avoid them for sensitive operations.”  
    - “Custom schemes are insecure; prefer verified App Links.”  
    - “Deep link parameters must be validated server-side; never trust client-provided tokens.”

    ## 3.5.4 Manifest Hardening — Summarized Notes (notes.md)

- ✅ **Purpose:**  
  Manifest Hardening ensures `AndroidManifest.xml` enforces secure defaults, removes legacy weaknesses, and minimizes attack surface. It covers global security hygiene beyond individual components.

---

### Key Hardening Areas

- **Component Exposure:**  
  - Explicitly declare `android:exported` for all components.  
  - Default to `false` unless there is a business need.  
  - Android 12+ requires explicit exported flags for components with intent-filters.

- **Debug & Development Flags:**  
  - `android:debuggable="true"` → critical risk if left in prod (attackers can attach debuggers, dump memory, extract secrets).  
  - Always strip debug flags in release builds.

- **Backup & Data Extraction:**  
  - `android:allowBackup="true"` allows adb backups of private app data.  
  - Set `allowBackup="false"` unless secure backups are a required feature.  
  - Use `android:fullBackupContent` to exclude sensitive files if backups are needed.

- **Task & Process Isolation:**  
  - Misusing `taskAffinity` can enable **task hijacking**: attacker activities appear inside the same task stack, leading to phishing.  
  - For sensitive activities (login, payments), set:  
    - `android:taskAffinity=""` (empty string)  
    - `android:excludeFromRecents="true"`  
    - `android:launchMode="singleTask"` or `singleInstance`  
  - Avoid `android:sharedUserId` (deprecated, insecure cross-app permission sharing).

- **ContentProvider Restrictions:**  
  - Do not leave providers exported or with broad authorities.  
  - Use `FileProvider` instead of exposing raw file paths.  
  - Restrict `grantUriPermissions` to specific URIs and keep grants temporary.

- **Network Security Config:**  
  - Avoid `cleartextTrafficPermitted="true"` → forces HTTP, exposes sensitive data.  
  - Best practice: disable cleartext traffic, enforce TLS, and use certificate pinning when needed.

- **Permission Hygiene:**  
  - Use the least-privilege model.  
  - Define custom permissions with appropriate levels (prefer `signature` for inter-app trust).  
  - Remove unused or overly broad permissions.

- **Metadata & Logs:**  
  - Avoid embedding secrets, API keys, or test flags in the manifest.  
  - Ensure no leftover debug/test components are declared in production.

---

### Common Misconfigurations (Pentester Focus)

- `allowBackup="true"` → attackers dump DBs via adb backup.  
- `debuggable="true"` → attackers attach debugger in production.  
- `taskAffinity` misused → phishing / task hijacking.  
- `cleartextTrafficPermitted="true"` → credentials or tokens exposed.  
- Exported components with no permission → remote abuse.

---

### OWASP Mapping

- A01 — Broken Access Control (exported components, task hijacking).  
- A02 — Sensitive Data Exposure (cleartext traffic, backups).  
- A05 — Security Misconfiguration (debuggable, allowBackup).  

---

### Interview One-Liners

- “Manifest Hardening is about enforcing least privilege globally.”  
- “`allowBackup=true` and `debuggable=true` are classic low-effort, high-severity findings.”  
- “TaskAffinity misconfigurations lead to task hijacking — a subtle but powerful phishing vector.”  
- “Cleartext traffic allowed in 2025 is an instant report — enforce TLS and pinning.”