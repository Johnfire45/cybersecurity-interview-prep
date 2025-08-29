# 3.2.1 Activities & Lifecycle

---

## What is an Activity?
- **Activity = a single UI screen** in an Android app.
- Examples: Login screen, Settings page, Profile screen.
- Apps can have multiple activities, linked via **Intents**.

---

## Activity Lifecycle
1. **onCreate()** → Activity created.  
2. **onStart()** → Activity becomes visible.  
3. **onResume()** → Foreground, user can interact.  
4. **onPause()** → Partially covered, loses focus.  
5. **onStop()** → Fully hidden.  
6. **onDestroy()** → Terminated.  
7. **onRestart()** → Before `onStart()` when resuming.

---

## Security Implications
- **Task Hijacking**  
  - Malicious apps overlay fake activities (e.g., fake banking login).  
  - Used for phishing and credential theft.

- **Intent Spoofing**  
  - Attacker sends malicious Intents to exported activities.  
  - Can trigger sensitive activities without authorization.

- **Activity Leaks**  
  - Activities mistakenly marked as `exported` in Manifest.  
  - Exposes debug or internal activities to attackers.

---

## Real-World Example
- **Marcher Trojan** (banking malware):  
  - Hijacked tasks by overlaying fake login activity.  
  - Stole credentials and then returned user to real app → unnoticed.  
- Google mitigated via:  
  - `taskAffinity` restrictions.  
  - `FLAG_SECURE` to prevent overlays/screenshots.

---

## Interview One-Liners
- “Activity = a UI screen, managed by lifecycle callbacks.”  
- “Lifecycle mismanagement → task hijacking or intent spoofing risks.”  
- “Exported activities in Manifest can leak sensitive features.”

# 3.2.2 Services & Background Processing

---

## What is a Service?
- Component for **tasks without UI** (music, sync, notifications).  
- Types: **Started**, **Bound**, **Foreground**.  

---

## Lifecycle
- `onCreate()`, `onStartCommand()`, `onBind()`, `onDestroy()`.  
- Restart modes: `START_STICKY`, `START_NOT_STICKY`, `START_REDELIVER_INTENT`.  

---

## Security Risks
- **Exported services** → can be abused by other apps.  
- **Bound service abuse** → attackers invoke methods if unprotected.  
- **Intent injection** → malicious extras exploited.  
- **Foreground misuse** → fake notifications to persist.  
- **Sticky misuse** → used for stealth persistence.  

---

## Defenses
- Set `exported=false` (default from Android 12+).  
- Protect exported services with `android:permission`.  
- Validate caller identity (`Binder.getCallingUid()`).  
- Use **WorkManager/JobScheduler** for background tasks.  

---

## Interview One-Liners
- “Since Oreo, background services need foreground mode or scheduling.”  
- “Exported services are dangerous unless protected with permissions.”  
- “WorkManager is the secure way for reliable background

# 3.2.3 Broadcast Receivers & System Events

---

## What is a Broadcast Receiver?
- Component that **listens for system-wide or app-wide events (Broadcasts)**.  
- Examples:  
  - `BOOT_COMPLETED` → after device boot.  
  - `BATTERY_LOW` → low battery warning.  
  - `SMS_RECEIVED` → incoming SMS.  

---

## Types of Broadcasts
- **Normal Broadcasts** → asynchronous, delivered in undefined order.  
- **Ordered Broadcasts** → delivered one at a time, receivers can modify/abort.  
- **Sticky Broadcasts** (deprecated) → persisted, later receivers could access last broadcast.  

---

## Security Risks
- **Broadcast Injection**  
  - Exported receivers can be triggered by any app with spoofed broadcasts.  
- **Data Exposure**  
  - Ordered broadcasts may be intercepted/modified (e.g., SMS hijacking).  
- **Privilege Escalation (Confused Deputy)**  
  - Privileged apps act on unverified broadcasts from untrusted sources.  

---

## Mitigations
- Set `android:exported=false` unless needed.  
- Protect receivers with `android:permission`.  
- Use **explicit intents** for targeted broadcasts.  
- Validate sender identity at runtime (`getCallingUid()`).  
- Use app-local alternatives (e.g., LiveData, in-app messaging).  

---

## Real-World Example
- **SMS Hijacking (2011)**:  
  - Malware registered high-priority `SMS_RECEIVED` receivers.  
  - Intercepted OTP SMS before legit apps could read them.  
  - Google fixed by restricting SMS/MMS broadcasts to default SMS apps.  

---

## Interview One-Liners
- “Broadcast Receivers handle system/app events like boot, battery, SMS.”  
- “Exported receivers are a common attack surface for broadcast injection.”  
- “Ordered broadcasts can leak or be hijacked if not secured.”  



# 3.2.4 Content Providers & Data Sharing

---

## What is a Content Provider?
- Component that **manages and shares structured data** between apps.  
- Common backends: SQLite, files, or cloud.  
- Examples:  
  - `content://contacts/` → contacts database.  
  - `MediaStore` → images, videos, audio.

---

## Security Risks
- **Exported Providers** → if unprotected, any app can read/write sensitive data.  
- **Improper Permissions** → loose or combined read/write permissions expose too much.  
- **SQL Injection** → raw query building from unvalidated input.  
- **Path Traversal/File Disclosure** → insecure file-serving providers leak system/user files.

---

## Mitigations
- **Use permissions correctly**:  
  - `readPermission` vs `writePermission`.  
  - Prefer **signature-level** permissions for sensitive data.  
- **Validate inputs**:  
  - Sanitize queries, use parameterized statements.  
- **Restrict export**:  
  - `android:exported=false` unless absolutely necessary.  
- **Use FileProvider**:  
  - For secure file sharing with `content://` URIs.  
- **Least Privilege**:  
  - Share only minimal data needed.

---

## Real-World Example
- Several apps (2019–2020) leaked **SMS logs, call history, and files** via exported providers.  
- Attackers accessed `content://` URIs directly to exfiltrate sensitive data.

---

## Interview One-Liners
- “Content Providers are data-sharing interfaces, but exported ones are high-risk.”  
- “Always enforce strict read/write permissions and validate all queries.”  
- “Use FileProvider for safe file sharing instead of raw paths.”