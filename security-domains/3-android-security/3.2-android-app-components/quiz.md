### Q4 — Subjective: Exported LoginActivity Misconfiguration

**Question:**  
An Android banking app accidentally exports its `LoginActivity` in the Manifest. Explain how an attacker could exploit this misconfiguration, and how a developer should secure the activity.

---

**Answer (Summary):**

1. **How an Attacker Exploits It**  
- Any malicious app can send an explicit Intent to launch `LoginActivity`.  
- Users may be tricked into entering credentials in an unintended context.  
- Security checks relying on normal app flow could be bypassed.

2. **How to Secure It**  
- Set `exported=false` in `AndroidManifest.xml` for sensitive activities.  
- If export is required:  
  - Use `android:permission` to restrict access.  
  - Validate calling package (`getCallingPackage()`) before handling.  
  - Apply `FLAG_SECURE` to prevent overlays/screenshots.

---

**Interview One-Liner**  
“An exported LoginActivity lets attackers launch it directly and bypass app flow. Developers must disable export or enforce permissions and validate origin.”

### Q4 — Subjective: Exported Background Service in a Financial App

**Question:**  
A financial app uses a background service to sync transactions. The service is accidentally marked `exported=true` in the Manifest. Explain how an attacker could abuse this misconfiguration, and what the developer should do to secure the service.

---

**Answer (Summary):**

1. **Abuse by Attacker**  
- Any app can send an explicit Intent to trigger the exported service.  
- Malicious apps could force transaction syncs or misuse sensitive operations.  
- Bound services could allow attackers to call internal methods directly.

2. **How to Secure**  
- Set `exported=false` for internal services.  
- If sharing is required:  
  - Protect with `android:permission` (prefer `signature`).  
  - Validate callers with `Binder.getCallingUid()`.  
  - Sanitize all Intent extras.  
- Prefer **WorkManager** for background tasks instead of raw services.

---

**Interview One-Liner**  
“An exported service is an open entry point for attackers. Always disable export or enforce strict permissions and caller validation.”

### Q5 — Subjective: Background Service Limits in Android 8.0

**Question:**  
Why did Android 8.0 introduce strict background service limits, and how do these changes improve both security and battery life compared to older Android versions?

---

**Answer (Summary):**

1. **Before Android 8.0**  
- Background services could run indefinitely with no restrictions.  
- Attackers abused this for stealth persistence (spyware, miners).  
- Resulted in high CPU, RAM usage, and battery drain.

2. **Android 8.0+ Restrictions**  
- Apps can’t run arbitrary background services.  
- Must use **Foreground Services** (with notification) or **WorkManager/JobScheduler** for scheduled jobs.

3. **Improvements**  
- **Security**: Prevents stealth malware, enforces user visibility via notifications, stops hidden persistence.  
- **Battery/Performance**: Conserves resources, prevents runaway background tasks.

---

**Interview One-Liner**  
“Android 8.0 restricted background services to foreground mode or schedulers, stopping stealth malware and improving battery efficiency.”

### Q4 — Subjective: Exported BOOT_COMPLETED Receiver

**Question:**  
An app has a Broadcast Receiver listening for `BOOT_COMPLETED` so it can auto-start services after reboot. The receiver is marked `exported=true` with no protection. Explain how an attacker could exploit this, and how the developer should secure it.

---

**Answer (Summary):**

1. **Exploitation by Attacker**  
- Any app can spoof the `BOOT_COMPLETED` broadcast.  
- Victim app auto-starts services unnecessarily.  
- Attacker can repeatedly trigger broadcasts to drain battery/resources.  
- Can also trigger sensitive background tasks like syncing or data uploads.

2. **How to Secure**  
- Set `exported=false` unless strictly required.  
- If needed, protect with `android:permission` (e.g., `RECEIVE_BOOT_COMPLETED`).  
- Validate sender identity at runtime (UID/package).  
- Prefer explicit or local broadcasts for internal flows.

---

**Interview One-Liner**  
“An exported BOOT_COMPLETED receiver enables spoofed reboot events, forcing services to start. Developers must disable export or enforce strict permissions.”

### Q5 — Subjective: Ordered Broadcast Security Risks

**Question:**  
Why are ordered broadcasts considered riskier than normal broadcasts, and how can a malicious app exploit them?

---

**Answer (Summary):**

1. **Why Riskier**  
- Normal broadcasts → asynchronous, delivered in no fixed order, cannot be altered.  
- Ordered broadcasts → delivered one by one in priority order.  
- Each receiver can **modify or abort** the broadcast.

2. **Exploitation by Attacker**  
- Malicious app registers a **high-priority receiver**.  
- Intercepts sensitive broadcasts (e.g., `SMS_RECEIVED`) before the legitimate app.  
- Can read OTPs, modify intent data, or block delivery entirely.

---

**Interview One-Liner**  
“Ordered broadcasts are risky because receivers can intercept, modify, or abort them, enabling attackers to hijack data like OTPs before legit apps receive it.”

### Q1 — Content Provider Security

**Question:**  
What is the recommended way to share files securely between Android apps?

**Answer:**  
Use **FileProvider with `content://` URIs**, which prevents raw path disclosure and supports scoped, temporary access grants.

### Q4 — Subjective: Exported Content Provider in Messaging App

**Question:**  
A messaging app exposes a Content Provider for storing SMS messages. The provider is marked as `exported=true` with no permissions. Explain how an attacker could exploit this misconfiguration, and how the developer should secure the provider.

---

**Answer (Summary):**

1. **Exploitation by Attacker**  
- Any app can query the provider’s `content://` URI.  
- Malicious apps can **read SMS messages**, including OTPs.  
- If write operations are exposed, attacker can insert, update, or delete records.  
- Real impact: OTP theft → financial fraud.

2. **How to Secure**  
- Set `android:exported=false` unless inter-app access is required.  
- Use strict `readPermission` and `writePermission` (prefer **signature-level**).  
- Validate all query inputs to prevent SQL Injection.  
- Use **FileProvider** or in-app sharing instead of exposing raw data.

---

**Interview One-Liner**  
“An exported provider without permissions lets attackers steal OTPs or modify data. Secure it with `exported=false`, signature-level permissions, and strict query validation.”