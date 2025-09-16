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