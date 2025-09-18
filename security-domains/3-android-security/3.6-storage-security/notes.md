## 3.6.1 SharedPreferences Security — Summarized Notes (notes.md)

- ✅ **Purpose:**  
  SharedPreferences is for storing small key-value pairs (app settings, flags). Data is stored as plaintext XML in `/data/data/<package>/shared_prefs/`.

- ❌ **Risks:**  
  - Plaintext storage of sensitive data (tokens, passwords, keys).  
  - Legacy insecure modes (`MODE_WORLD_READABLE`, `MODE_WORLD_WRITEABLE`).  
  - Tampering with flags (e.g., `isPremiumUser=true`).  
  - Extraction via adb backup if `allowBackup=true`.  
  - Harvesting by malware/rooted devices.

- 🧩 **Real-World Scenarios:**  
  - Token theft → session hijacking.  
  - Credential harvesting from prefs.  
  - Privilege escalation → flip `isAdmin` or premium flags.  
  - Attacker modifies values to bypass paywalls or enable features.

- 🧪 **Pentester Checklist:**  
  - Static: look for `getSharedPreferences` usage in code.  
  - Dynamic: inspect `/shared_prefs/*.xml` on emulator/device.  
  - Check if sensitive data persists after logout.  
  - Test backup extraction if `allowBackup=true`.

- 🛡️ **Best Practices:**  
  - Never store credentials or tokens in SharedPreferences.  
  - Use **EncryptedSharedPreferences** + Keystore for sensitive data.  
  - Disable backups (`allowBackup=false`) unless securely implemented.  
  - Enforce access control server-side, not via local flags.  
  - Integrity check prefs that influence business logic.

- 🔗 **OWASP Mapping:**  
  - A02 — Sensitive Data Exposure  
  - A05 — Security Misconfiguration  
  - A01 — Broken Access Control

- 💬 **Interview One-Liner:**  
  “SharedPreferences is plaintext XML — safe for settings, not secrets. Use Keystore-backed encrypted storage for sensitive data.”

  ## 3.6.2 Internal Storage Security — Summarized Notes (notes.md)

- ✅ **Definition:**  
  Internal storage = private app directory (`/data/data/<package_name>/`) for files, databases, preferences, and cache. Sandbox-enforced by Android (UID-based).

- ⚠️ **Risks:**  
  - Rooted/compromised devices bypass sandbox → data dump.  
  - `allowBackup=true` → adb backups leak internal files.  
  - Misconfigured file permissions (world-readable/writeable).  
  - Plaintext storage of tokens, passwords, or PII.  
  - Sensitive data persisting in logs or cache.

- 🧩 **Common Pentest Findings:**  
  - Secrets stored in plaintext files or databases.  
  - Credit card/PII stored unencrypted.  
  - Session tokens remain after logout.  
  - World-readable legacy files.  
  - Sensitive logs under `/files/` or `/cache/`.

- 🛡️ **Best Practices:**  
  - Store minimal/safe data in internal storage.  
  - Encrypt sensitive files with Keystore-backed keys or EncryptedFile APIs.  
  - Disable backups (`allowBackup=false`) if sensitive files exist.  
  - Never use world-readable/writeable modes.  
  - Clear caches/logs on logout or uninstall.

- 🧪 **Pentester Checklist:**  
  - Inspect `/files/`, `/databases/`, `/cache/` for secrets.  
  - Check file permissions and modes.  
  - Confirm encryption of sensitive data.  
  - Verify sensitive data removal after logout/uninstall.

- 💬 **Interview One-Liner:**  
  “Internal storage is private by default, but root, adb backups, or misconfigurations turn it into a data leak — always encrypt sensitive files and disable backups.”

  ## 3.6.3 External Storage Security — Summarized Notes (notes.md)

- ✅ **Definition:**  
  External storage = shared locations (e.g., `/sdcard/`, `/storage/emulated/0/`) accessible by multiple apps. Used for large files and inter-app sharing.

- ❌ **Risks:**  
  - Any app with `READ_EXTERNAL_STORAGE` / `WRITE_EXTERNAL_STORAGE` can access files.  
  - Sensitive data exposure if tokens, logs, or PII are stored here.  
  - Tampering with config/data files → malicious app can alter behavior.  
  - Data persists after uninstall → privacy risk.  
  - World-readable media files → accessible by gallery/file manager apps.

- 🧩 **Common Pentest Findings:**  
  - Tokens, certificates, or API responses written to `/sdcard/`.  
  - Logs or databases left in external storage.  
  - Config files controlling premium features stored insecurely.  
  - Medical/finance apps leaving sensitive reports in shared folders.

- 🛡️ **Best Practices:**  
  - Never store sensitive data in external storage.  
  - Use internal storage or app-specific dirs via `getExternalFilesDir()`.  
  - Adopt Scoped Storage (Android 10+) for sandboxing external files.  
  - Apply file-level encryption if storage outside sandbox is unavoidable.  
  - Sanitize and validate all files read from external storage.  
  - Ensure sensitive files are cleared on logout/uninstall.

- 🧪 **Pentester Checklist:**  
  - Inspect `/sdcard/` and `/storage/emulated/0/` for sensitive files.  
  - Check persistence of files after uninstall.  
  - Test tampering by modifying external files.  
  - Review app behavior under Scoped Storage rules.

- 💬 **Interview One-Liner:**  
  “External storage is a public hallway — never store secrets there; use scoped storage or encryption for sensitive files.”

  ## 3.6.4 SQLite Database Security — Summarized Notes (notes.md)

- ✅ **Definition:**  
  SQLite is the default relational database for Android apps. Databases are stored in `/data/data/<package>/databases/`.

- ⚠️ **Risks:**  
  - Databases are plaintext by default → sensitive data exposure if device rooted or backup enabled.  
  - Tokens, passwords, or PII stored unencrypted.  
  - SQL injection via unsafe `rawQuery()` or string concatenation.  
  - World-readable databases in legacy apps (improper permissions).  
  - Residual deleted data recoverable without vacuuming.  
  - Debug tables or logs left in production DBs.

- 🧩 **Common Pentest Findings:**  
  - Plaintext passwords/tokens in DB tables.  
  - Sensitive PII stored without encryption.  
  - SQL injection through ContentProviders.  
  - Old debug data accessible in DB.  
  - Session tokens not cleared on logout.

- 🛡️ **Best Practices:**  
  - Do not store passwords or long-lived tokens.  
  - Encrypt DBs with SQLCipher or EncryptedDatabase APIs; store keys in Keystore.  
  - Always use parameterized queries (`?` with selectionArgs).  
  - Restrict ContentProviders; apply `pathPermissions` and permissions.  
  - Disable backups (`allowBackup=false`) if DB contains sensitive data.  
  - Purge old data and vacuum DB.  
  - Remove debug/test tables from production.

- 🧪 **Pentester Checklist:**  
  - Inspect `/databases/` for sensitive content in plaintext.  
  - Search for `rawQuery()` with concatenation in code.  
  - Test ContentProviders for injection or excessive exposure.  
  - Verify encryption and key management.  
  - Check whether sensitive records persist after logout.

- 💬 **Interview One-Liner:**  
  “SQLite DBs are plaintext by default — pentesters look for stored secrets and injection risks, while secure apps encrypt DBs, use Keystore keys, and sanitize queries.”

## 3.6.5 Android Keystore Security — Summarized Notes (notes.md)

- ✅ **Definition:**  
  Android Keystore provides a secure system-managed container for cryptographic keys. Keys are non-exportable and can only be used via Keystore APIs (encrypt, decrypt, sign). On modern devices, keys are stored in hardware-backed secure modules (TEE/StrongBox).

- ⚠️ **Risks & Misuses:**  
  - Developers storing raw AES keys in code, SharedPreferences, or DB instead of Keystore.  
  - Using software-backed Keystore only → vulnerable on rooted/compromised devices.  
  - Hardcoding keys in APKs → trivial extraction.  
  - Poor crypto practices (ECB mode, static IVs, weak padding).  
  - Exporting wrapped keys or caching decrypted secrets in plaintext files/logs.  

- 🧩 **Access & Location:**  
  - Apps cannot access other apps’ Keystore keys — isolation is enforced by the system.  
  - Keystore keys are not stored in app directories. They are managed under system-owned locations (e.g., `/data/misc/keystore/`) or inside secure hardware.  
  - Root/system-level access may compromise software-backed keys but not hardware-backed ones.  

- 🧪 **Pentester Checklist:**  
  - Check if keys are hardcoded in APK or prefs instead of Keystore.  
  - Verify use of `KeyGenParameterSpec` and secure modes (AES-GCM, RSA OAEP, ECDSA).  
  - Look for insecure crypto (ECB mode, static IVs).  
  - Ensure app uses hardware-backed Keystore if available.  
  - Check if decrypted data is cached or written to logs.  

- 🛡️ **Best Practices:**  
  - Generate keys directly inside Keystore with `KeyGenParameterSpec`.  
  - Use hardware-backed (TEE/StrongBox) Keystore where available.  
  - Use strong algorithms and secure modes (AES-GCM, RSA-OAEP/PSS).  
  - Never export raw keys; keep all operations inside Keystore.  
  - Rotate keys periodically; use short-lived session tokens.  
  - Avoid caching decrypted secrets in memory or files.  
  - Combine Keystore with EncryptedSharedPreferences / EncryptedFile for secure storage.  

- 🔗 **OWASP Mapping:**  
  - A02 — Sensitive Data Exposure (keys stored insecurely).  
  - A05 — Security Misconfiguration (wrong crypto modes, weak Keystore usage).  

- 💬 **Interview One-Liner:**  
  “Android Keystore is a vault where keys never leave secure hardware; misuse happens when developers hardcode or export keys — always generate inside Keystore, use hardware-backed AES-GCM/RSA, and avoid plaintext caching.”
  
  ## 3.6.6 Cryptography Misuse — Summarized Notes (notes.md)

- ✅ **Definition:**  
  Cryptography misuse = insecure use of crypto algorithms, keys, or libraries in Android apps. Weak crypto nullifies protection even if used with Keystore.

- ⚠️ **Common Misuses:**  
  - Hardcoded keys/IVs in code, prefs, or DB.  
  - Insecure algorithms: MD5, SHA-1, DES, 3DES, RC4.  
  - Weak modes: AES/ECB leaks data patterns.  
  - Static or predictable IVs/salts → ciphertext correlation.  
  - Insecure RNG: `Random` instead of `SecureRandom`.  
  - Custom or home-grown crypto instead of vetted libraries.  
  - Poor key management: long-lived keys, no rotation, keys outside Keystore.

- 🧩 **Real-World Risks:**  
  - APK decompiled → hardcoded AES key recovered → decrypt all user data.  
  - AES/ECB used → ciphertext reveals repeating patterns (e.g., card numbers).  
  - Predictable IVs → attackers correlate encrypted data across sessions.  
  - Passwords hashed with MD5 → trivial dictionary attacks.  
  - Global compromise if same static key used across installs.

- 🛡️ **Best Practices:**  
  - Use modern algorithms: AES-GCM, RSA-OAEP/PSS, SHA-256/512, PBKDF2/Bcrypt/Argon2.  
  - Always use `SecureRandom` for keys/IVs.  
  - Never hardcode or store raw keys outside Keystore.  
  - Generate unique IVs per encryption operation.  
  - Rotate and expire keys regularly; avoid indefinite reuse.  
  - Use authenticated encryption (AES-GCM) to ensure integrity + confidentiality.  
  - Do not invent custom crypto — rely on proven libraries.

- 🧪 **Pentester Checklist:**  
  - Decompile APK → search for hardcoded keys, constants, weak crypto APIs.  
  - Inspect `Cipher.getInstance()` for insecure modes (e.g., ECB).  
  - Verify randomness source (`SecureRandom` vs `Random`).  
  - Test if ciphertext is deterministic → red flag for missing IVs.  
  - Check if keys are rotated or static.  

- 💬 **Interview One-Liner:**  
  “Crypto misuse often comes from hardcoded keys, weak algorithms, or wrong modes — secure apps use Keystore-managed keys, AES-GCM, SecureRandom, and proven libraries only.”