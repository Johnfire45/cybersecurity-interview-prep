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