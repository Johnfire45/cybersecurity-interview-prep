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

