### 🧨 Real-World Scenario — Privilege Escalation via `find` in sudoers

Your organization's Linux servers include the following line in `/etc/sudoers.d/devs`:

```
%devs ALL=(ALL) NOPASSWD: /usr/bin/find
```

A Red Team operator was able to escalate to root using this configuration.

#### Q:  
How could this misconfiguration lead to privilege escalation, and what mitigation would you recommend?

---

#### ✅ Answer:
Allowing `find` with root privileges (and no password) enables abuse because `find` can execute arbitrary commands via the `-exec` flag. An attacker in the `devs` group can escalate by running:

```bash
sudo find / -exec /bin/bash \;
```

This executes a root shell, fully compromising the system.

---

#### 🛡️ Mitigations:
- Avoid granting `sudo` access to binaries like `find`, `vim`, or `less` that support command execution
- If `sudo` access is required, restrict to **safe, non-interactive subcommands** using `Cmnd_Alias`
- Require password prompt (`NOPASSWD` is dangerous on interactive commands)

---

#### 🔍 Detection Strategies:
- Monitor `/var/log/auth.log` or journald for suspicious `sudo` executions (e.g., `find -exec`)
- SIEM correlation rules to flag unusual binary usage under elevated privileges

---

#### 📚 Bonus:
Other commonly misused binaries for sudo privilege escalation:
- `vim`
- `less`
- `awk`
- `perl`
- `tar`
- `python`

---

## 🔐 Topic: Linux Fundamentals — File Permissions & Privilege Escalation

### 🧨 Real-World Scenario

Your organization has a shared directory `/opt/scripts/` that is writable by members of the `devteam` group. Inside this directory exists a script named `deploy.sh`, which is executable via `sudo` without requiring a password:

```bash
(devteam) ALL=(ALL) NOPASSWD: /opt/scripts/deploy.sh
```

### ❓ Question

What is the **most critical security risk** introduced by this configuration?

- A. Members of `devteam` can run `deploy.sh` without a password  
- B. The `deploy.sh` script can be modified by any `devteam` user, potentially leading to root privilege escalation  
- C. The `deploy.sh` script is stored outside the `/usr/bin` directory  
- D. Users in `devteam` can audit the output of the script via log files  

---

### ✅ Correct Answer: **B**

### 💡 Explanation:

- ✅ The script resides in a **writable directory**, allowing users to **modify or replace the file** with a malicious version.
- ✅ The script is executed with **`sudo` without password**, meaning **modified contents run as root**.
- ⚠️ This opens a path for **privilege escalation**, such as:
  - Injecting reverse shells
  - Modifying critical files
  - Adding unauthorized users
- ❌ The issue is not that `devteam` can run the script, but that they can **alter what gets run with root privileges**.

---

### 🛠️ Mitigation:

- ✅ Move scripts to a **non-writable directory** (e.g., `/usr/local/sbin`)
- ✅ Lock down permissions (`root:root`, `0755`)
- ✅ Remove `NOPASSWD` where not strictly required
- ✅ Audit all `sudoers` entries that refer to user-controlled paths
- ✅ Implement file integrity monitoring or AppArmor/SELinux enforcement

### 🧨 Real-World Scenario — Reverse Shell via Misconfigured Cron Job

**Scenario:**  
You are a security analyst in a fintech company. During a routine investigation, you notice a strange network connection from one of your internal development servers to an unknown external IP on port `4444`. The team insists no services should be communicating outbound on that port. Upon deeper inspection, the `/opt/devops/scripts/deploy.sh` file was recently modified by a developer. This file is run frequently via cron and has `sudo` permissions without password prompt.

You also find that:
- The `devteam` group has write access to `/opt/devops/scripts/`
- A process tree shows:  

cron ── sh ── python ── bash

---

**Q:**  
What is the most likely security issue here, and how would you investigate and respond to confirm a potential reverse shell?

---

**A:**  
The key issue is a **privilege escalation risk** due to the writable `deploy.sh` script executed with `sudo` via cron. A malicious insider (or compromised developer account) could inject a **reverse shell payload** (e.g., Python one-liner) into the script. Since cron executes it with elevated privileges, this allows attackers to gain root access and establish an external connection.

**Indicators:**
- `devteam` has write access to sensitive executable scripts
- Cron executes the script silently and recurrently
- Process tree showing unexpected outbound behavior (`python → bash`)
- No alerting or audit mechanism is in place

**Recommended Actions:**
- 🔍 Check last modified time of `deploy.sh`
- 🧾 Review cron job schedules and user context
- 📜 Audit bash history and auth logs
- 🌐 Correlate outbound connection logs with known IOCs
- 🔒 Remove group write permissions on scripts in cron context
- 🛡️ Enforce `sudo` policies with password + logging
- ⚠️ Introduce real-time alerts for outbound traffic on uncommon ports

---

**Q:**  
During an investigation, you discover that a suspicious database exfiltration occurred around 02:00 AM.  
As the security engineer, which Linux log file would you check first, and what other logs/tools would you review to confirm and correlate the incident?  

---

**A (Your Answer):**  
I would check `/var/log/auth.log` first, since it records authentication attempts like SSH logins and sudo usage.  

---

**Correct Answer (Comparison):**  
- ✅ **First check:** `/var/log/auth.log` (authentication attempts, sudo usage, privilege escalation).  
- 🔹 **Then correlate with:**  
  - `/var/log/syslog` or `/var/log/messages` (system events, cron jobs, daemon activity).  
  - `/var/log/audit/audit.log` (if auditd enabled, shows sensitive file access).  
  - Web/service logs (e.g., `/var/log/nginx/access.log` or DB logs).  
  - Network activity logs (`kern.log`, IDS alerts, or packet captures).  
- 🔹 **Commands/Tools:**  
  - `grep "02:" /var/log/auth.log` for filtering.  
  - `journalctl --since "2025-08-20 01:30:00" --until "2025-08-20 02:30:00"`.  
  - `ausearch` if auditd is enabled.  

**Comparison Note:**  
Your answer correctly identified `auth.log` as the starting point.  
To be interview-ready, also mention **system, audit, web/service, and network logs** for full incident correlation.  