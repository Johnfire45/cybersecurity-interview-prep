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