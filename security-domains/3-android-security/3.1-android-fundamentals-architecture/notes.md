# 3.1.1 Android OS Architecture

---

## Overview
- Android is a layered architecture built on the **Linux kernel**, tailored for mobile devices.
- Goal: isolate apps, enforce permissions, provide hardware abstraction, and ensure security.

---

## Layers of Android Architecture

1. **Linux Kernel (Foundation)**
   - Services: process scheduling, memory mgmt, power mgmt, networking, drivers (camera, audio, Wi-Fi, Bluetooth).
   - Security: UID/GID isolation, SELinux for mandatory access control.
   - Example: A camera app’s access request goes through kernel-level camera driver, controlled by SELinux policy.

2. **Hardware Abstraction Layer (HAL)**
   - Standard API interface between hardware drivers and Android framework.
   - Example: Camera HAL ensures all apps access the camera consistently, regardless of vendor hardware.
   - Makes Android portable across Samsung, Pixel, OnePlus, etc.

3. **Android Runtime (ART) & Core Libraries**
   - Executes app code: compiles DEX bytecode into native instructions (AOT + JIT).
   - Core libraries (libc, SSL, SQLite, WebKit) provide app functionality.
   - Example: WhatsApp uses SQLite for chat storage, executed via ART.

4. **Application Framework**
   - High-level APIs for developers.
   - Components:
     - **Activity Manager** → handles activity lifecycle.
     - **Package Manager** → manages installs, updates, app signatures.
     - **Window Manager** → renders UI.
     - **Content Providers** → share structured data between apps.
     - **Telephony/Location Services** → access device radios, GPS.
   - Example: Uber app relies on Location Manager, Facebook uses Content Providers to share media.

5. **Applications (Top Layer)**
   - **System Apps**: Pre-installed with firmware, signed with platform key.  
     - Examples: Phone, Contacts, Settings, default Messaging.  
     - Google Calendar → System app if bundled with Pixel firmware.  
   - **User Apps**: Installed by user from Play Store/APK.  
     - Examples: WhatsApp, Instagram, TikTok.  
     - Google Calendar → User app if installed later from Play Store.
   - Apps communicate securely via **Binder IPC**.

---

## Security Features
- **Sandboxing**: Each app runs as a separate Linux process with unique UID.
- **Permission Model**: Explicit permission requests enforced at runtime.
- **Binder IPC Security**: Mediates inter-process calls securely.
- **SEAndroid**: SELinux policies enforce mandatory access control.
- **Verified Boot**: Cryptographically ensures OS/firmware integrity at boot.

---

## Real-World Security Failures
- **Privilege Escalation**: Kernel bugs used by malware to escape sandbox.
- **Insecure HALs**: Vendor camera/audio drivers exploited (common in older Android).
- **Rooting**: User/attacker disables sandbox & SELinux enforcement.
- **Binder IPC Abuse**: Apps exposing insecure Content Providers → data theft.

---

## Interview Lens
- Q: *How does Linux kernel secure Android apps?*  
  A: UID sandboxing + SELinux enforcement isolate apps.  
- Q: *What ensures Android works across multiple OEM devices?*  
  A: HAL layer.  
- Q: *Why ART replaced Dalvik?*  
  A: Performance boost, lower battery drain, memory efficiency.  
- Q: *System vs User app example?*  
  A: Google Calendar → System app if bundled, User app if installed manually.

---

## Diagram Reference: Android OS Architecture (Detailed)

**Applications (Top Layer)**  
- Examples: WhatsApp, Instagram (user apps), Phone, Settings (system apps).  
- Runs in sandbox, isolated by UID.  
- Communicates via Binder IPC.  

**Application Framework**  
- Provides APIs to apps.  
- Components: Activity Manager, Package Manager, Window Manager, Content Providers.  
- Example: Uber uses Location Manager, Facebook uses Content Providers.  

**Android Runtime (ART) + Core Libraries**  
- Executes DEX code → native instructions.  
- Libraries: libc (system), SSL (crypto), SQLite (databases).  
- Example: WhatsApp → SQLite DB for chats.  

**Hardware Abstraction Layer (HAL)**  
- API layer bridging Android to vendor hardware.  
- Example: Camera HAL → ensures consistent API for different cameras.  

**Linux Kernel (Base Layer)**  
- Drivers: camera, Wi-Fi, audio, Bluetooth.  
- Security: Process isolation, SELinux, Verified Boot.  
- Example: Kernel enforces camera access only for apps with CAMERA permission.  

# 3.1.1 Android OS Architecture — Tabular Diagram

| **Layer**                  | **Key Functions**                                                                 | **Examples**                                                                                  | **Security Role**                                                                 |
|-----------------------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| **Applications (Top Layer)** | User & System apps running in sandbox                                             | - User Apps: WhatsApp, Instagram, TikTok <br> - System Apps: Phone, Contacts, Settings <br> - Google Calendar = System app if pre-installed, User app if downloaded | - Sandbox isolation per app <br> - Binder IPC for secure communication            |
| **Application Framework**   | Provides APIs & managers for app lifecycle, UI, services                          | - Activity Manager (manages lifecycle) <br> - Package Manager (installs/verifies apps) <br> - Location Manager (GPS) <br> - Content Providers (data sharing) | - Enforces permission checks <br> - Manages inter-app communication securely      |
| **Android Runtime (ART) + Core Libraries** | Executes app code, provides libraries                                 | - ART (replaced Dalvik VM) → JIT + AOT compilation <br> - Libraries: libc, SSL, SQLite, WebKit <br> - WhatsApp uses SQLite for chats | - Memory mgmt <br> - Secure execution of DEX code <br> - Provides crypto libraries |
| **Hardware Abstraction Layer (HAL)** | Standard API between hardware drivers and higher Android layers          | - Camera HAL (consistent camera API across devices) <br> - Audio HAL <br> - Sensor HAL | - Controls access to hardware <br> - Prevents direct driver exposure to apps      |
| **Linux Kernel (Foundation)** | Core OS layer: process mgmt, drivers, power, networking, SELinux                 | - Drivers: Wi-Fi, Camera, Audio, Bluetooth <br> - Process scheduling, memory management | - UID/GID process isolation <br> - SELinux Mandatory Access Control <br> - Verified Boot |

# 3.1.2 App Sandboxing & Isolation

---

## What is Sandboxing?
- Sandboxing = isolating apps so they can’t freely interact with each other or the system.  
- Every app runs in its **own process** with a **unique Linux UID**.  
- Prevents one app from directly accessing another app’s data.  

---

## How It Works
1. **UID Isolation**  
   - Unique UID per app assigned at install.  
   - Kernel enforces → one UID cannot read/write another’s files.  

2. **Filesystem Isolation**  
   - Each app gets private dir in `/data/data/<package_name>`.  
   - Only owning UID can access.  

3. **Process Isolation**  
   - Apps run in separate processes.  
   - A crash/compromise doesn’t directly affect others.  

4. **Binder IPC**  
   - Secure inter-process communication.  
   - Enforces permission checks for data/service exchange.  

---

## Security Reinforcements
- **SELinux**: Fine-grained mandatory access control on top of UID sandboxing.  
- **Permissions**: Explicit consent needed for shared resources (camera, contacts).  
- **Isolated Processes**: Sensitive components can run with restricted capabilities.  

---

## Benefits
- Limits attack surface.  
- Malware can’t easily spread between apps.  
- Enforces **least privilege**.  

---

## Weaknesses
- **Privilege Escalation**: Kernel exploits (e.g., Dirty COW) can escape sandbox.  
- **Shared UID**: Apps with same dev key can share UID → weakens isolation.  
- **Misconfigured IPC**: Exposed Content Providers can bypass sandbox.  

---

## Interview One-Liners
- “Sandboxing is enforced by Linux UID separation + SELinux policies.”  
- “Apps communicate via Binder IPC, controlled by permissions.”  
- “Weaknesses include kernel exploits, shared UID abuse, and misconfigured IPC.”

# 3.1.3 Android Security Model

---

## Core Principle
- Based on **least privilege** → apps only get the permissions they need.
- Security enforced across **kernel, framework, and app levels**.

---

## Key Components

1. **Linux Kernel Security**
   - UID/GID isolation for sandboxing.
   - SELinux (SEAndroid) → Mandatory Access Control (MAC) policies.

2. **Application Sandbox**
   - Each app in its own isolated environment.
   - Data stored in `/data/data/<package_name>` → private to app.

3. **Permission Model**
   - Apps declare permissions in `AndroidManifest.xml`.
   - Runtime permissions (Android 6.0+) require user approval.
   - Example: Camera, Location.

4. **App Signing**
   - Every app signed by developer certificate.
   - Ensures app integrity & developer identity.
   - Tampered apps rejected if signature invalid.

5. **Inter-Process Communication (IPC)**
   - Binder IPC mediates communication.
   - Permission checks enforced for Content Providers, Broadcasts, Intents.

6. **Google Play Protect**
   - Malware scanning in Play Store & on-device.
   - Detects harmful behavior at runtime.

7. **Verified Boot**
   - Cryptographically checks OS images at boot.
   - Prevents tampered system partitions from loading.

8. **Updates & Patch Management**
   - Monthly patches from Google/OEMs.
   - Project Treble improves modular patch delivery.

---

## Enforcement Mechanisms
- **DAC**: Linux file ownership & permissions.
- **MAC**: SELinux strict policy enforcement.
- **Cryptography**: App signing, Verified Boot.
- **User Control**: Runtime permission prompts, Play Protect alerts.

---

## Real-World Failures
- **Stagefright (2015)** → exploited media framework; mitigated by SELinux.
- **Fake ID Vulnerability** → exploited cert chain validation to impersonate trusted apps.
- **Kernel exploits (e.g., Dirty COW)** → enabled sandbox escapes.

---

## Interview One-Liners
- “Android’s security model combines sandboxing, permissions, app signing, SELinux, and Verified Boot.”  
- “App signing enforces integrity and ties apps to a developer identity.”  
- “Least privilege: each app runs with unique UID + explicit permission requests.”  

# 3.1.4 App Lifecycle & Process Management

---

## Why Lifecycle Matters
- Android has **limited resources** (battery, RAM, CPU).  
- OS manages app processes efficiently for performance + security.  
- Lifecycle ensures apps behave predictably when switching, minimizing, or resuming.

---

## App Component Lifecycles

1. **Activity Lifecycle**  
   - States: `onCreate()` → `onStart()` → `onResume()` → `onPause()` → `onStop()` → `onDestroy()`.  
   - Example: Switching apps moves current app to `onPause()`/`onStop()`.

2. **Service Lifecycle**  
   - For background tasks.  
   - States: `onCreate()` → `onStartCommand()` → `onDestroy()`.  
   - Example: Music service plays audio even if UI closed.

3. **Broadcast Receiver Lifecycle**  
   - Triggered by system events (e.g., SMS, battery low).  
   - Lives only during `onReceive()` execution.

4. **Content Provider Lifecycle**  
   - Manages shared app data (e.g., Contacts DB).  
   - Initialized when accessed.

---

## Process Management

- **Zygote Process**  
  - Parent processs that starts at the boot
  - Forks new app processes from a preloaded template.  
  - Ensures faster startup.

- **Low Memory Killer (LMK)**  
  - Kills processes when memory is low.  
  - Priority order: Foreground > Visible > Service > Background > Empty.

- **Oom_adj & cgroups**  
  - Assigns “out-of-memory” scores.  
  - Foreground apps = lowest score (least likely to be killed).

---

## Security Aspects
- Background services restricted → prevents spyware persistence.  
- Lifecycle limits reduce risk of hidden processes draining battery.  
- Broadcast receivers short-lived → smaller attack surface.

---

## Real-World Examples
- **Task Killers:** Early apps harmed battery by fighting Android’s lifecycle.  
- **Android 8+ Restrictions:** Limited background services to prevent abuse (e.g., hidden crypto miners).  
- **Malware Workarounds:** Some malware re-register services; modern Android detects/blocks such persistence.

---

## Interview One-Liners
- “Zygote forks app processes to speed up launches.”  
- “LMK prioritizes killing background apps to free memory.”  
- “Lifecycle management enforces least privilege and reduces persistent malware risks.”

# Zygote Workflow in Android

---

## Overview
- **Zygote = the mother of all Android app processes.**
- Started very early at boot by the `init` process.
- Preloads core Java classes, Android framework classes, and common resources.
- Listens for requests to start new apps.
- When requested, it **forks** itself to create new app processes.

---

## How Zygote Works
1. **Init Process**  
   - Starts Zygote at boot time.  

2. **Zygote Process**  
   - Preloads classes & libraries (Java core, Android framework).  
   - Ensures consistency across all apps.  

3. **App Launch Request**  
   - System requests Zygote to start an app.  

4. **Fork**  
   - Zygote forks itself into a child process.  

5. **Child Process**  
   - Inherits preloaded resources.  
   - Saves memory & speeds up startup.  

6. **App Initialization**  
   - Child process loads app-specific code/resources.  
   - App is now ready to run.  

---

## Security Implications
- Zygote runs with **system-level privileges**.  
- If compromised:  
  - Every child process inherits the exploit.  
  - Could lead to **system-wide privilege escalation**.  
- Protections:  
  - Zygote socket restricted to system services.  
  - SELinux policies enforce strict access control.  

---

## Real-World Example
- **Zygote Vulnerability (2012):**  
  - Malicious apps could send crafted requests to Zygote socket.  
  - Triggered endless forking → **Denial of Service (DoS)**.  
  - Patched by restricting access.  

---

## Interview One-Liners
- “Every Android app process is forked from Zygote for speed and efficiency.”  
- “Zygote preloads libraries so apps launch faster and consume less memory.”  
- “If Zygote is compromised, every app process inherits the attack — breaking sandboxing.”  
- “Android protects Zygote via SELinux and socket restrictions.”  

---

## Diagram (Mermaid)

```mermaid
flowchart TD
    A[Init Process] --> B[Zygote Process<br/>Preloads libraries & framework]
    B --> C[App Launch Request]
    C --> D[Fork: Zygote creates child process]
    D --> E[Child Process<br/>Inherits preloaded resources]
    E --> F[App Initialization<br/>Loads app code & resources]