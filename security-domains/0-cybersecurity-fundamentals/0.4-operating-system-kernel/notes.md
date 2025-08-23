# 0.4.1 Linux Init Systems (systemd, init.d)

## 1. Overview
- **Init system** = first process started by kernel (`PID 1`) after boot.  
- Responsible for:
  - Initializing environment  
  - Starting/stopping services (daemons)  
  - Managing service lifecycles  

---

## 2. Historical Init Systems

### SysVinit (init.d)
- Scripts in /etc/init.d/, run via /etc/inittab.  
- Runlevels:
  - 0 → halt  
  - 1 → single-user  
  - 3 → multi-user (no GUI)  
  - 5 → multi-user + GUI  
  - 6 → reboot  
- Example:  
  service sshd start  
  /etc/init.d/sshd restart  

**Limitations:** sequential boot, no auto-restart if service crashes.  

---

### Upstart
- Event-driven init (Ubuntu 9–14).  
- Config in /etc/init/.  
- Replaced by systemd.  

---

### systemd (modern standard)
- Default on most modern distros (Ubuntu ≥15, RHEL 7+, Debian 8+).  
- Features:
  - Parallel startup → faster boot  
  - Dependency management  
  - Logging via journald  
  - Timers (can replace cron)  
  - Resource control with cgroups  
- Unit files in /etc/systemd/system/*.service  

**Key commands:**  
- systemctl start nginx → start  
- systemctl enable nginx → enable at boot  
- systemctl status docker → status  
- journalctl -u nginx → logs  

**Example service unit:**  
[Unit]  
Description=Example Service  
After=network.target  

[Service]  
ExecStart=/usr/bin/python3 /opt/app.py  
Restart=always  

[Install]  
WantedBy=multi-user.target  

---

## 3. Security Relevance
- **Persistence:** Attackers add malicious .service or .timer.  
- **Privilege Escalation:** Weak ExecStart abused.  
- **Defensive Actions:**  
  - Monitor /etc/systemd/system/*.service for changes.  
  - Restrict systemctl enable usage.  
  - Use systemctl is-enabled to review enabled services.  

---

## 4. Interview One-Liners
- *Difference:* init.d = sequential scripts, no monitoring. systemd = parallel, dependencies, logging, monitoring.  
- *Attack angle:* persistence via malicious services/timers.  
- *Defense:* baseline & monitor service/timer unit files.  

# 0.4.2 Linux Boot Process

## 1. Overview
The Linux boot process is the sequence of steps from powering on the system to reaching a usable login shell.  
It has five major stages: BIOS/UEFI → Bootloader → Kernel → Init system → User space.

---

## 2. Boot Process Stages

### Stage 1: BIOS / UEFI Initialization
- Firmware runs POST (Power-On Self Test).  
- Finds bootable device (based on boot order).  
- Passes control to bootloader in MBR or EFI partition.  

---

### Stage 2: Bootloader
- Common bootloaders: GRUB2, LILO, systemd-boot.  
- Functions:  
  - Load kernel into memory.  
  - Provide boot menu & recovery options.  
  - Pass parameters (root FS, runlevel/target) to kernel.  
- **Security:** Bootloader tampering → persistence. Secure Boot verifies authenticity.  

---

### Stage 3: Kernel Initialization
- Kernel (`vmlinuz`) loaded into memory.  
- Initializes drivers, mounts root filesystem (read-only).  
- Starts `init` (or `systemd`) as PID 1.  
- Boot messages viewable via `dmesg`.  

---

### Stage 4: Init System (PID 1)
- Init (SysVinit or systemd) takes over.  
- Responsibilities:  
  - Mounts remaining filesystems.  
  - Starts daemons & services.  
  - Sets runlevels (SysVinit) or targets (systemd).  
- Example: `systemctl default` sets the default target.  

---

### Stage 5: User Space / Login
- Init launches login processes:  
  - `getty` (TTY) or display manager (GUI).  
- User enters credentials → shell (bash/zsh) starts.  

---

## 3. Security Relevance
- **Attack Vectors:**  
  - Bootkits / rootkits in BIOS, UEFI, or bootloader.  
  - Modified initrd or kernel params disabling protections (e.g., SELinux).  
- **Defenses:**  
  - Secure Boot (signed bootloader & kernel).  
  - Restrict physical access (BIOS password, disable boot from USB).  
  - Boot integrity monitoring (TPM, remote attestation).  

---

## 4. Interview One-Liners
- *Boot sequence:* BIOS/UEFI → Bootloader → Kernel → Init → User space.  
- *Persistence risk:* Attackers can compromise bootloader, init scripts, or kernel modules.  
- *Defense:* Secure Boot, least privilege, monitor boot integrity.  

# 0.4.3 Kernel Modules & Syscalls

## 1. Kernel Modules
- Loadable pieces of code extending kernel functionality (drivers, filesystems, security tools).  
- Located in `/lib/modules/<kernel-version>/`.  
- Common commands:  
  - `lsmod` → list loaded modules  
  - `modprobe <module>` → load a module  
  - `rmmod <module>` → remove a module  
  - `insmod <file.ko>` → insert a custom module  

**Security Risks:**  
- Attackers can insert **malicious kernel modules** (rootkits).  
- Rootkits can hide processes, files, or network connections.  
- Example: Loadable Kernel Module (LKM) rootkits.  

---

## 2. Syscalls (System Calls)
- Interface between **user space** and the **kernel**.  
- Applications use syscalls to interact with OS and hardware.  
- Examples:  
  - `open()` — open a file  
  - `read()` / `write()` — file I/O  
  - `fork()` — create process  
  - `execve()` — execute program  
  - `socket()` — network communication  

**Security Risks:**  
- Attackers hook syscalls to hide activity (e.g., hide processes from `ps`).  
- Exploiting buggy syscalls → privilege escalation (e.g., Dirty COW exploit).  
- Misuse can bypass sandboxing or auditing.  

---

## 3. Security Risks Overview
- **Malicious Kernel Modules:** Persistence, stealth, and system control.  
- **Syscall Hooking:** Fake results returned to user-space tools.  
- **Privilege Escalation:** Kernel exploits abusing vulnerable syscalls.  

---

## 4. Defense Strategies
- Enforce **kernel module signing** (allow only signed modules).  
- Restrict `modprobe`, `lsmod`, and `rmmod` usage to root/admins.  
- Monitor system integrity with tools (`rkhunter`, `chkrootkit`).  
- Apply **SELinux/AppArmor** to control module behavior.  
- Monitor syscalls with `strace`, `auditd`, or **seccomp profiles**.  

---

## 5. Interview One-Liners
- *Kernel modules:* “Extend kernel at runtime but can be abused by rootkits.”  
- *Syscalls:* “They are the user-to-kernel interface; attackers exploit or hook syscalls for persistence.”  
- *Defense:* “Use module signing, audit syscalls, enforce seccomp, and monitor for rootkits.”  

# 0.4.4 Memory & Filesystem Hierarchies

## 1. Memory Management

### Memory Layout
- **User Space (per process):**
  - Text segment → program code
  - Data segment → global/static variables
  - Heap → dynamic memory (malloc, free)
  - Stack → function calls, local variables

- **Kernel Space (shared):**
  - Protected from user processes
  - Handles syscalls, drivers, I/O, scheduling

### Virtual Memory
- Each process sees its own memory space
- Kernel maps virtual → physical using paging & MMU
- Swap space extends memory on disk

### Security Relevance
- **Exploits:** buffer overflow, stack smashing, heap spraying
- **Privilege Escalation:** kernel memory corruption
- **Mitigations:** ASLR (randomized layout), DEP (non-executable memory), stack canaries

---

## 2. Filesystem Hierarchy (FHS Standard)

### Key Directories
- `/` → Root directory
- `/bin` → Essential binaries (ls, cp, mv, cat)
- `/sbin` → System binaries (iptables, ifconfig)
- `/etc` → Configuration files (passwd, shadow)
- `/var` → Variable data (logs, mail, spool)
- `/tmp` → Temporary files (world-writable, cleared on reboot)
- `/usr` → User applications & libraries
- `/lib` → Libraries for `/bin` and `/sbin`
- `/home` → User directories
- `/boot` → Kernel, initrd, GRUB configs
- `/dev` → Device files
- `/proc` → Virtual FS for processes/kernel info
- `/sys` → Kernel and device information

### Security Relevance
- `/etc/shadow` → target for password hashes
- `/tmp` → attacker persistence (world-writable)
- `/proc` & `/sys` → leak system info
- `/boot` → bootloader tampering risk

---

## 3. Attacker vs Defender

**Attacker Tactics**
- Hide malware in `/tmp`, `/dev/shm`
- Replace binaries in `/bin` or `/sbin`
- Steal password hashes from `/etc/shadow`
- Tamper `/boot/grub.cfg` for persistence

**Defender Strategies**
- File integrity monitoring (`aide`, `tripwire`)
- Protect `/etc` and `/boot` with strict permissions
- Mount `/tmp` with `noexec,nosuid`
- Monitor `/var/log` for anomalies
- Regularly inspect `/proc` and `/sys`

---

## 4. Interview One-Liners
- *Memory:* User space (heap, stack) vs kernel space — isolation critical for security
- *Exploits:* Overflows and kernel bugs can lead to root; mitigated by ASLR/DEP
- *Filesystem:* `/etc` = configs, `/var` = logs, `/tmp` = risk, `/boot` = persistence vector
- *Attacker vs Defender:* Attackers abuse `/tmp`, `/shadow`, `/boot`; defenders enforce permissions and monitor integrity

# 0.4.5 Chain of Trust in Bootloaders

## 1. What is Chain of Trust?
- Ensures each boot stage executes only **verified and trusted code**.  
- Root of trust begins in immutable hardware/firmware (TPM, ROM).  
- Each subsequent stage (firmware → bootloader → kernel → OS) is cryptographically validated.

---

## 2. Stages of Trust in Boot
1. **Hardware Root of Trust**
   - Immutable trust anchor in hardware (TPM, Intel Boot Guard, ARM TrustZone).  

2. **Firmware (BIOS/UEFI)**
   - Verifies bootloader signature using embedded keys.  
   - UEFI Secure Boot databases store trusted keys.  

3. **Bootloader (GRUB, systemd-boot)**
   - Signed bootloader validated by firmware.  
   - Bootloader validates the **kernel** before loading.  

4. **Kernel & Initrd**
   - Kernel image validated by bootloader.  
   - Kernel enforces trust on modules/initrd.  

5. **OS & Applications**
   - Kernel + integrity frameworks (IMA, SELinux, AppArmor) enforce trust in user space.

---

## 3. Importance
- Prevents bootkits/rootkits.  
- Blocks persistence at early boot stages.  
- Guarantees system integrity **from power-on to user space**.

---

## 4. Risks if Chain is Broken
- Malicious/modified bootloader (GRUB tampering).  
- Kernel booted with insecure params (e.g., `selinux=0`).  
- Unsigned kernel modules loaded.  
- Stealth persistence with rootkits at boot level.

---

## 5. Defender Strategies
- Enable **UEFI Secure Boot**.  
- Use **signed bootloaders** and **signed kernels**.  
- Protect `/boot` with strict permissions & integrity monitoring.  
- Enforce TPM-based attestation to detect tampering.  
- Monitor GRUB configs for unauthorized kernel params.

---

## 6. Interview One-Liners
- *Definition:* “Chain of Trust ensures every boot stage verifies the next before execution.”  
- *Root of Trust:* Starts with hardware/firmware → extends to bootloader, kernel, OS.  
- *Security Risk:* Breaking the chain enables stealth bootkits/rootkits.  
- *Defense:* Secure Boot, signed components, TPM attestation.