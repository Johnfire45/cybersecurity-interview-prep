# linux-fundamental
## 0.3.1 Linux CLI Essentials – Summary Notes

## Why It Matters
- Core skill for security analysts, pentesters, and system defenders.
- Critical for log analysis, malware hunting, forensics, and automation.

## Essential Commands

| Command | Purpose |
|----------|---------|
| `cat` | View file contents |
| `grep` | Search for patterns in text |
| `find` | Locate files based on criteria |
| `awk` | Process text by column |
| `cut` | Trim text to specific fields |
| `ls` | List directory contents |
| `chmod`, `chown` | Manage file permissions and ownership |
| `head`, `tail` | View start/end of files |
| `wc` | Count words, lines, or characters |
| `sort`, `uniq` | Organize and de-duplicate data |
| `xargs` | Pass output as input to other commands |
| `tee` | Output to file and screen simultaneously |
| `diff` | Compare files to detect changes |

## Security Use Cases
- Log forensics
- Malware hunting
- Detecting privilege escalation risks
- Monitoring system changes
- Building security automation

## Real-World Example

- **Find all SUID binaries (common for privilege escalation):**

```bash
find / -perm -4000 -type f 2>/dev/null
```

- **Extract failed SSH login attempts:**

```bash
grep "Failed password" /var/log/auth.log
```


## 🔐 Linux File Permissions & Ownership

### 📁 Ownership Model
- Every file is associated with:
  - **User (owner)** — who created or owns the file
  - **Group** — group of users with shared access
  - **Others** — everyone else on the system

### 🛡️ Permission Types
- **Read (r)** — View contents
- **Write (w)** — Modify contents
- **Execute (x)** — Run file or enter directory
- Represented as: `-rwxr-xr--`

### 🔢 Numeric Mode (Octal)
- `chmod 755 file`
  - 7 = rwx (owner)
  - 5 = r-x (group)
  - 5 = r-x (others)

### 🧰 Key Commands
- `chmod` — change permission
- `chown` — change ownership
- `chgrp` — change group
- `ls -l` — list with permissions

### 🧬 Special Permissions
- **Setuid (u+s):** Run with owner's privilege
- **Setgid (g+s):** Files inherit group ID
- **Sticky bit (+t):** Only owner can delete (used in `/tmp`)

### 🧾 Default Permissions & umask
- Files: `666 - umask`
- Dirs: `777 - umask`
- View with `umask`

### 🔍 Extended Permissions
- ACLs using `getfacl` / `setfacl` for fine-grained control

### 0.3.3 Users, Groups, and Sudo Privileges — Summary Notes

- **Users**: Each user on a Linux system is identified by a unique UID. Stored in `/etc/passwd`.
- **Groups**: Groups define collections of users with shared permissions. Stored in `/etc/group`.
- **Primary vs Secondary Groups**:
  - Primary group is assigned at login.
  - Secondary groups allow access to additional resources.
- **User Management**:
  - `useradd`, `usermod`, `userdel`, `passwd` for managing users.
- **Group Management**:
  - `groupadd`, `groupmod`, `groupdel`, `gpasswd` for managing groups.
- **Sudo**:
  - `sudo` allows delegated privileged access.
  - Configured via `/etc/sudoers` (edited with `visudo`).
  - Can be restricted per user or command.
- **Security Implications**:
  - Avoid giving full `sudo` access.
  - Log all `sudo` usage using syslog.
  - Never allow password-less sudo for sensitive commands unless required.

  ## 📂 0.3.4 Process Management (ps, top, kill)

### 🔍 Process Inspection
- `ps aux` / `ps -ef`: Lists all processes with key info (PID, CPU, memory, user).
- Used to trace rogue or unauthorized processes (e.g., malware or reverse shells).

### 📊 Real-Time Monitoring
- `top`: Interactive view of running processes; press `P`/`M` to sort by CPU/memory.
- `htop`: Enhanced version with colorized UI and easy kill option.

### 🔫 Process Termination
- `kill -15 <PID>`: Graceful termination (SIGTERM).
- `kill -9 <PID>`: Force kill (SIGKILL).
- `killall <name>`: Kills all instances of a process by name.

### 🎛️ Process Prioritization
- `nice`: Launch process with set priority.
- `renice`: Modify priority of existing process (lower nice = higher priority).

### 🧠 Job Control
- `&`, `jobs`, `fg`, `bg`, `Ctrl+Z`: Manage foreground and background jobs in shell sessions.

### 🛡️ Security Angle
- Monitor for suspicious processes (e.g., reverse shells, long-running scripts).
- Restrict background execution and monitor users spawning unauthorized processes.

### 🟠 Logs & Syslog Analysis

- **Purpose:** Logs are the “black box” of Linux. They help in detecting attacks, troubleshooting failures, and providing compliance evidence.
  
- **Syslog (rsyslog/syslog-ng):**
  - Routes logs by **facility** (auth, cron, kern, etc.) and **severity** (0=emerg → 7=debug).
  - Example: `authpriv.* /var/log/auth.log`
  - Logs are stored in plain text files under `/var/log/*`.

- **systemd-journald:**
  - Modern binary logging system, queried with `journalctl`.
  - Supports filtering by service, time, severity. Example:  
    `journalctl -u sshd -p err --since "1h ago"`

- **Common log locations:**
  - Debian/Ubuntu → `/var/log/syslog`, `/var/log/auth.log`
  - RHEL/CentOS → `/var/log/messages`, `/var/log/secure`
  - Journald persistent logs → `/var/log/journal/`

- **Auditd (Security-focused):**
  - Captures syscall/file events. Example:  
    `-w /etc/sudoers -p wa -k sudoers_changes`
  - Search with `ausearch`, summarize with `aureport`.

- **Log rotation & retention:**
  - Controlled by `logrotate`. Prevents disk fill and keeps only recent, compressed logs.
  - Retention depends on compliance requirements (e.g., HIPAA may need months/years).

- **Security use-cases:**
  - Detect brute force attempts → check `/var/log/auth.log` or `journalctl -u sshd`.
  - Investigate privilege escalation → `journalctl _COMM=sudo -p err`.
  - Detect persistence via cron → `/var/log/cron` or `journalctl -u cron`.
  - Spot suspicious service changes via `journalctl -u <service>`.

**Key takeaway:**  
Know where logs are stored, how to query them with `journalctl`, and how to set up forwarding (rsyslog → SIEM). Auditd is critical for detailed security monitoring.

# 0.3.6 Crons, Daemons, and Services

## 1. Daemons
- Background processes, not tied to user logins.  
- Examples: `sshd`, `httpd/nginx`, `mysqld`.  
- Launched at boot, managed by init systems (systemd, init.d).  

## 2. Services
- Daemons registered with the init system.  
- Managed with `systemctl` (start, stop, enable, disable, restart).  
- Security risk: attackers may create malicious services for persistence.  

## 3. Cron Jobs
- Scheduler for repetitive tasks.  
- Config: `/etc/crontab`, user crontabs (`crontab -e`).  
- Syntax: 5 time fields + command.  
- Example: `0 2 * * * /usr/local/bin/backup.sh` → daily at 2 AM.  
- Security risk: attackers use cron jobs for persistence.  

## 4. At & Anacron
- `at`: schedule one-time tasks.  
- `anacron`: runs periodic tasks even if the system was off.  

## 5. Security Relevance
- Persistence vector: malicious crons or services.  
- Defender tasks: audit cron jobs, list services, validate binaries.  
- Maps to OWASP Top 10: **Security Misconfiguration**.  

# 0.3.7 Package Managers (apt, yum, snap, etc.)

## 1. Overview
- Package managers automate installing, updating, configuring, and removing software.  
- Handle:
  - Dependencies
  - Verification (signatures, GPG keys)
  - Updates & patching

---

## 2. Major Package Managers
- **APT (Debian/Ubuntu)**
  - `.deb` packages
  - Config: `/etc/apt/sources.list`
  - Commands: `apt install`, `apt update`, `apt upgrade`

- **YUM / DNF (RHEL/CentOS/Fedora)**
  - `.rpm` packages
  - Config: `/etc/yum.repos.d/`
  - Commands: `yum install`, `dnf update`

- **Snap (Ubuntu)**
  - Self-contained, sandboxed packages
  - Command: `snap install <package>`

- **Others**
  - `zypper` (SUSE)
  - `pacman` (Arch Linux)

---

## 3. Security Risks
- **Malicious Repositories**
  - Adding untrusted repos can deliver backdoored packages.

- **Dependency Confusion / Typosquatting**
  - Malicious package mimics trusted one (e.g., `libssl-update` vs `libssl`).

- **Unsigned Packages**
  - Installing without verifying GPG signatures can lead to tampered software.

- **Supply Chain Attacks**
  - Compromise of repos or mirrors → mass infection.
  - Real-world analogies: SolarWinds, Codecov.

---

## 4. Security Best Practices
- Use only **official, signed repos**.  
- Verify GPG signatures for `.deb` and `.rpm`.  
- Restrict outbound access → allow-list trusted mirrors.  
- Monitor installation logs:
  - APT: `/var/log/apt/history.log`
  - YUM: `/var/log/yum.log`
- Keep system updated with security patches.  

---

## 5. Security Mapping
- **MITRE ATT&CK:** T1195 Supply Chain Compromise, T1072 External Remote Services  
- **OWASP:** A08 Software & Data Integrity Failures, A05 Security Misconfiguration  

---

## 6. Interview One-Liners
- **Attacker’s View:** “Abuse package managers by typosquatting, malicious repos, or poisoning the supply chain.”  
- **Defender’s View:** “Enforce signed repos, baseline sources, monitor logs, and restrict outbound update traffic.”  

# 0.3.8 Bash Scripting Basics

## 1. Overview
- **Bash (Bourne Again Shell):** command interpreter + scripting language for Linux/Unix.  
- Used for automation, system jobs (cron/daemons), and quick security tooling.  

---

## 2. Core Concepts

### Variables
name="Harshit"  
echo "Hello $name"  

- `$VAR` → variable substitution.  
- Always use quotes `"${VAR}"` to prevent injection.  

### Conditionals
if [ -f /etc/passwd ]; then  
  echo "File exists"  
else  
  echo "Not found"  
fi  

- `[ -f file ]` → file exists  
- `[ -d dir ]` → directory exists  

### Loops
for user in $(cat /etc/passwd); do  
  echo $user  
done  

- Used for iterating lists, files, IP ranges.  

### Functions
function greet() {  
  echo "Hello $1"  
}  
greet "Harshit"  

- Reusable blocks of code.  

### File Operations & Redirection
echo "test" > file.txt     # overwrite  
echo "append" >> file.txt  # append  
cat file.txt | grep "app"  # filter  

- `>` overwrite, `>>` append, `2>` redirect errors  

---

## 3. Common Tools in Scripts
- `grep` — pattern matching  
- `awk` — text processing  
- `sed` — search/replace  
- `cut`, `sort`, `uniq` — parsing & deduplication  

Example:  
cat access.log | awk '{print $1}' | sort | uniq -c  
→ Count unique IPs from logs  

---

## 4. Security Pitfalls
- **Command Injection**  
  eval $user_input   # 🚨 dangerous  
  → Never run untrusted input  

- **Unquoted Variables**  
  cat $file   # unsafe if $file="; rm -rf /"  
  ✅ Always quote → cat "$file"  

- **Set Secure Options**  
  set -euo pipefail  
  - `-e` exit on error  
  - `-u` treat unset vars as error  
  - `-o pipefail` catch pipeline errors  

---

## 5. Real-World Security Use Cases
- **Red Team**  
  - Automate brute-force, payload generation, log parsing.  
- **Blue Team**  
  - Monitor logs, extract suspicious IPs, automate IOC collection.  

---

## 6. Security Mapping
- **MITRE ATT&CK:**  
  - T1059.004 — Command & Scripting Interpreter: Bash  
  - T1053 — Scheduled Task/Job (when used in crons)  
- **OWASP:**  
  - A08 — Software and Data Integrity Failures (unsafe scripting practices)  

---

## Interview One-Liner
“Bash scripting automates tasks but also introduces risks.  
Safe scripting requires quoting variables, avoiding `eval`, and enforcing secure shell options.  
Attackers abuse Bash for persistence and automation, while defenders use it for monitoring and IOC detection.”