### Q1 — Subjective: Stagefright Vulnerability & Android OS Architecture

**Question:**  
During the Stagefright vulnerability (2015), attackers could exploit the Android media framework by sending a crafted MMS.  

Explain:  
1. How the Android OS architecture (sandboxing + SELinux) helped reduce the impact.  
2. What would have happened if Android relied only on DAC (Discretionary Access Control) without SELinux?

---

**Answer (Summary):**

1. **With SELinux + Sandboxing**  
   - Each app runs in its own UID sandbox (Linux kernel).  
   - The vulnerable mediaserver was confined by SELinux (MAC) policies.  
   - Could only access camera/audio resources, not contacts, SMS, or system files.  
   - This limited the blast radius of the exploit.

2. **If Only DAC Was Used**  
   - DAC relies on user/group permissions (file ownership).  
   - Mediaserver had broad access under its UID.  
   - Compromise could allow attackers to read/write sensitive files, escalate privileges, and break the sandbox.  
   - DAC alone is too coarse to contain exploits.

---

**Interview One-Liner**  
“Android’s sandboxing plus SELinux policies confined Stagefright to mediaserver’s limited domain. With only DAC, attackers could have gained far broader access and escalated privileges.”

### Q2 — Subjective: Hardware Abstraction Layer (HAL) Security & Risks

**Question:**  
How does the Hardware Abstraction Layer (HAL) improve Android’s security and portability, and what risks are introduced if a vendor implements a poorly coded HAL driver?

---

**Answer (Summary):**

1. **How HAL Improves Security & Portability**  
   - **Portability:** Provides a standard API → apps work consistently across different vendors (Samsung, Pixel, OnePlus).  
   - **Security:** Prevents direct hardware access by apps → apps interact via HAL APIs, reducing attack surface.

2. **Risks of Poorly Coded HAL Drivers**  
   - **Reliability Risk:** Hardware may not work consistently (e.g., audio HAL bug prevents stable speaker/mic use).  
   - **Security Risk:** HAL drivers run with elevated privileges → vulnerabilities (e.g., buffer overflow, poor validation) can be exploited.  
     - Exploits in Qualcomm camera/audio HALs have enabled attackers to gain root access.  

---

**Interview One-Liner**  
“HAL abstracts hardware behind standard APIs for portability and protects it from direct app access. But weak HAL drivers create privileged attack surfaces that can be exploited for reliability issues and even root-level privilege escalation.”

### Q7 — Subjective: Verified Boot Disabled

**Question:**  
If Verified Boot is disabled on an Android device, what is the single most critical security risk introduced?

---

**Answer (Summary):**  
- **Verified Boot** cryptographically ensures the OS and firmware integrity at boot.  
- **If disabled**:
  - Attackers can boot a **tampered OS/kernel image**. 
  - Rootkits or persistent malware could survive reboots.  
  - The trust chain breaks → sandboxing and SELinux can be bypassed.  

---

**Interview One-Liner**
“Disabling Verified Boot allows tampered OS images or rootkits to load at boot, breaking the trust chain and compromising the entire Android security model.”

### Q4 — Subjective: Shared UID & Sandboxing

**Question:**
If two apps are signed by the same developer key and configured to share the same UID, how does this affect Android’s sandboxing model, and what security risk does it introduce?

---

**Answer (Summary):**  
- Normally, each app gets a **unique UID**, enforcing strict sandboxing and isolation.  
- If two apps share the same UID and are signed with the same key:  
  - They run under the **same Linux user context**.  
  - They can directly access each other’s data and processes, bypassing Binder IPC and permission checks.  
  - If one app is compromised, the attacker gains access to the other app as well.  

---

**Interview One-Liner**  
“Shared UIDs between apps break Android’s sandboxing by merging their security contexts, meaning compromise of one app exposes the other.”

### Q4 — Subjective: Shared UID & Sandboxing

**Question:**  
If two apps are signed by the same developer key and configured to share the same UID, how does this affect Android’s sandboxing model, and what security risk does it introduce?

---

**Answer (Summary):**  
- Normally, each app gets a **unique UID**, enforcing strict sandboxing and isolation.  
- If two apps share the same UID and are signed with the same key:  
  - They run under the **same Linux user context**.  
  - They can directly access each other’s data and processes, bypassing Binder IPC and permission checks.  
  - If one app is compromised, the attacker gains access to the other app as well.  

---

**Interview One-Liner**  
“Shared UIDs between apps break Android’s sandboxing by merging their security contexts, meaning compromise of one app exposes the other.”

### Q1 — Subjective: Background Service Persistence in Android 8+

**Question:**  
If a malicious app keeps restarting a background service to avoid being killed by Android’s process management, how does modern Android (Android 8.0 and above) prevent this kind of persistence, and why is this important for security?

---

**Answer (Summary):**

1. **Android 8+ Restrictions**  
- Background services are restricted and cannot run indefinitely.  
- Apps must use **Foreground Services** (with a visible notification) or **JobScheduler/WorkManager**.  
- If a service tries to persist secretly, the system kills it.

2. **Security Importance**  
- Prevents spyware and persistent malware from hiding in the background.  
- Reduces risks of stealth processes draining battery/memory.  
- Enforces visibility of long-running tasks → smaller attack surface.  

---

**Interview One-Liner**  
“Android 8+ blocks endless background services, forcing apps to use visible or scheduled tasks. This prevents spyware persistence and reduces the attack surface.”

### Q2 — Subjective: Zygote Process in Android

**Question:**  
Why does Android use the Zygote process for starting new app processes instead of creating each process from scratch, and what would be the security risk if Zygote itself were compromised?

---

**Answer (Summary):**

1. **Why Zygote is Used**  
- Zygote starts at boot and preloads core classes, libraries, and ART runtime.  
- New apps are forked from Zygote instead of starting fresh.  
- This makes app startup **faster, more efficient, and consistent**.

2. **Security Risk if Compromised**  
- All app processes are children of Zygote.  
- If Zygote is exploited:  
  - Every new app process inherits the compromise.  
  - Sandbox and process isolation would be broken.  
  - Leads to potential **system-wide privilege escalation**.

---

**Interview One-Liner**  
“Zygote speeds up app launches by preloading libraries and forking processes. If compromised, every new process inherits the exploit, collapsing Android’s sandboxing model.”