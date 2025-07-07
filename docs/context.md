# **LLM Context Version:** v2.1 (2025-01-XX)

# LLM Context for Cybersecurity Interview Preparation

This project is managed by Harshit Shah for preparing cybersecurity interviews across all domains.

## 🎯 Project Overview
This repository contains structured preparation for cybersecurity job interviews across 17 comprehensive domains:
- Cybersecurity Fundamentals (Core principles, Linux, Networking, Trust & Identity)
- Web Application Security (OWASP Top 10 focus)
- API Security  
- Android Security
- Client-side Security
- Network Security
- Cloud Security
- DevSecOps
- Secure Code Review
- Cryptography
- Red Team / Offensive Security
- Blue Team / Defensive Security
- Mobile Security (iOS)
- IoT & Embedded Security
- Social Engineering & Human Factors
- Compliance & Governance
- Career & Behavioral Prep

## 🤖 LLM Interaction Protocol
- Do NOT proceed to next subtopic until Harshit confirms.
- Do NOT provide feedback unless explicitly asked.
- Do NOT generate prompts unless Harshit requests a Cursor prompt.
- Always refer to this context.md file before acting.
- Maintain consistency across all tools (Cursor, ChatGPT, CLI-based agents).
- Act as a mentor, not an autonomous executor.
- Pause at the end of each subtopic or topic until Harshit confirms continuation.
- Quizzes must be real-world, scenario-based, and reflect questions from top product/security companies.
- Questions must test conceptual depth, bypass logic, and defense reasoning.
- Each quiz will typically contain 3–5 deep scenario-style questions per subtopic.

## 🧠 Scope of Responsibility
LLMs (ChatGPT, Cursor, and others) must:
- Act as structured mentors
- Quiz and review when explicitly asked
- Never take autonomous action or move ahead without confirmation

## 📚 Learning Protocol

1. Harshit will choose a topic and subtopic
2. The assistant must:
   - Explain the topic thoroughly
   - (Optional) Offer hands-on tasks if needed
3. **Only when confirmed:** conduct a quiz
4. Provide feedback **only if asked**
5. Wait for confirmation before advancing
6. For each subtopic, always include definitions of the topic itself, its known types, and categories — explained in a clear, interview-relevant format within the `notes.md` file.

## Progress Tracker Protocol

- Use `_progress.md` as the single source of truth for topic-wise progress tracking.
- Do not mark any topic or subtopic as completed unless confirmed by Harshit.
- If a topic is marked 🟢 Mastered in `_progress.md`, it implies theory, quiz, and hands-on were all completed and reviewed.
- Maintain parity between `context.md` guidelines and `_progress.md` statuses.

## 📂 Universal Topic Structure

Each subtopic folder must contain:
- `notes.md` – Explanation + diagrams
- `quiz.md` – Final answers to scenario questions
- `hands-on.md` – Cursor-based PoC or payload work
- `resources.md` – External links, labs, cheatsheets

Each domain (e.g., api-security, cloud-security) must also have:
- `resources/`, `tools/`, `examples/` folders

---

# ✅ Current Status (as of now)

- Project structure: ✅ initialized with 17 domains (folders 0–16 all present and correctly named)
- Quiz protocol: ✅ active
- Web Application Security: 🟢 Completed:
  - `1.1-input-validation-injection/` (Input Validation & Injection) — 🟢 COMPLETED
  - `1.2-cross-site-scripting/` (Cross-Site Scripting) — 🟢 COMPLETED
- Cybersecurity Fundamentals (Domain 0): 🟡 NEXT PRIORITY - Content development beginning
- Other domains: 🔴 Not Started

---

**📊 Content Status Summary:**
- **Completed:** 1.1 (Input Validation & Injection), 1.2 (Cross-Site Scripting)
- **Next Priority:** Domain 0 (Cybersecurity Fundamentals) - Core principles, Linux, Networking, Trust & Identity
- **Ready for Development:** All 17 domains with complete, correctly numbered folder structure.
- **Future Priority:** Continue with Domain 1 remaining subtopics (1.3-1.12) after Domain 0 completion.

---

## 📁 Folder Name Consistency Check

All domain and subtopic folder names from 0 to 16 match the `_progress.md` index exactly.

**No mismatches detected.**  
If you spot any, please flag for manual review.

## 📁 Complete Project Directory Structure

```
cybersecurity-interview-prep/
├── README.md                                    # Project overview and navigation
├── .gitignore                                   # Git exclusions
├── security-domains/                            # All security domains grouped here
│   ├── 0-cybersecurity-fundamentals/            # Domain 0: Cybersecurity fundamentals 🟡 NEXT PRIORITY
│   │   ├── 0.1-core-principles/                 # Subtopic 0.1 🔴 NOT STARTED
│   │   ├── 0.2-security-programs-frameworks/    # Subtopic 0.2 🔴 NOT STARTED
│   │   ├── 0.3-linux-fundamentals/              # Subtopic 0.3 🔴 NOT STARTED
│   │   ├── 0.4-operating-system-kernel/         # Subtopic 0.4 🔴 NOT STARTED
│   │   ├── 0.5-networking-fundamentals/         # Subtopic 0.5 🔴 NOT STARTED
│   │   ├── 0.6-trust-identity-concepts/         # Subtopic 0.6 🔴 NOT STARTED
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 1-web-application-security/              # Domain 1: Web application security 🟢 PARTIALLY COMPLETED
│   │   ├── 1.1-input-validation-injection/      # Subtopic 1.1 🟢 COMPLETED
│   │   ├── 1.2-cross-site-scripting/            # Subtopic 1.2 🟢 COMPLETED
│   │   ├── 1.3-authentication-session/          # Subtopic 1.3 🔴 NOT STARTED
│   │   ├── 1.4-authorization-access-control/    # Subtopic 1.4 🔴 NOT STARTED
│   │   ├── 1.5-file-upload-attacks/             # Subtopic 1.5 🔴 NOT STARTED
│   │   ├── 1.6-cors-security-headers/           # Subtopic 1.6 🔴 NOT STARTED
│   │   ├── 1.7-host-header-cache-poisoning/     # Subtopic 1.7 🔴 NOT STARTED
│   │   ├── 1.8-csrf-samesite/                   # Subtopic 1.8 🔴 NOT STARTED
│   │   ├── 1.9-business-logic-vulnerabilities/  # Subtopic 1.9 🔴 NOT STARTED
│   │   ├── 1.10-ssrf-redirects/                 # Subtopic 1.10 🔴 NOT STARTED
│   │   ├── 1.11-security-misconfiguration/      # Subtopic 1.11 🔴 NOT STARTED
│   │   ├── 1.12-misc-attacks/                   # Subtopic 1.12 🔴 NOT STARTED
│   │   ├── resources/                           # Domain-specific resources
│   │   ├── tools/                               # Domain-specific tools
│   │   └── examples/                            # Domain-specific examples
│   ├── 2-api-security/                          # Domain 2: API security
│   │   ├── 2.1-api-authentication-authorization/
│   │   ├── 2.2-api-input-validation-filtering/
│   │   ├── 2.3-api-authorization-flaws/
│   │   ├── 2.4-api-data-exposure/
│   │   ├── 2.5-api-security-testing/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 3-android-security/                      # Domain 3: Android security
│   │   ├── 3.1-app-manifest-analysis/
│   │   ├── 3.2-storage-security/
│   │   ├── 3.3-webview-security/
│   │   ├── 3.4-ipc-broadcast-security/
│   │   ├── 3.5-network-security/
│   │   ├── 3.6-reverse-engineering-obfuscation/
│   │   ├── 3.7-mobile-security-tools/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 4-client-side-security/                  # Domain 4: Client-side security
│   │   ├── 4.1-javascript-security/
│   │   ├── 4.2-dom-based-attacks/
│   │   ├── 4.3-content-security-policy/
│   │   ├── 4.4-same-origin-policy/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 5-network-security/                      # Domain 5: Network security
│   │   ├── 5.1-network-protocols/
│   │   ├── 5.2-tls-ssl-configuration/
│   │   ├── 5.3-man-in-the-middle-attacks/
│   │   ├── 5.4-network-security-tools/
│   │   ├── 5.5-wireless-security/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 6-cloud-security/                        # Domain 6: Cloud security
│   │   ├── 6.1-identity-access-management/
│   │   ├── 6.2-cloud-storage-security/
│   │   ├── 6.3-container-security/
│   │   ├── 6.4-serverless-security/
│   │   ├── 6.5-cloud-monitoring-logging/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 7-devsecops/                             # Domain 7: DevSecOps
│   │   ├── 7.1-secure-cicd-pipelines/
│   │   ├── 7.2-infrastructure-as-code-security/
│   │   ├── 7.3-security-testing-automation/
│   │   ├── 7.4-policy-as-code/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 8-secure-code-review/                    # Domain 8: Secure code review
│   │   ├── 8.1-code-review-methodologies/
│   │   ├── 8.2-common-vulnerabilities-in-code/
│   │   ├── 8.3-language-specific-security/
│   │   ├── 8.4-secure-development-practices/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 9-cryptography/                          # Domain 9: Cryptography
│   │   ├── 9.1-cryptographic-fundamentals/
│   │   ├── 9.2-tls-ssl-pki/
│   │   ├── 9.3-key-management/
│   │   ├── 9.4-cryptographic-vulnerabilities/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 10-red-team-offensive-security/          # Domain 10: Red team/offensive
│   │   ├── 10.1-web-exploitation/
│   │   ├── 10.2-mobile-exploitation/
│   │   ├── 10.3-network-exploitation/
│   │   ├── 10.4-social-engineering/
│   │   ├── 10.5-custom-exploits-scripts/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 11-blue-team-defensive-security/         # Domain 11: Blue team/defensive
│   │   ├── 11.1-siem-log-analysis/
│   │   ├── 11.2-threat-hunting/
│   │   ├── 11.3-incident-response/
│   │   ├── 11.4-detection-engineering/
│   │   ├── 11.5-security-operations/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 12-mobile-security-ios/                  # Domain 12: Mobile security (iOS)
│   │   ├── 12.1-ios-app-security/
│   │   ├── 12.2-ios-reverse-engineering/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 13-iot-embedded-security/                # Domain 13: IoT & embedded security
│   │   ├── 13.1-iot-protocols-communication/
│   │   ├── 13.2-firmware-analysis/
│   │   ├── 13.3-hardware-security/
│   │   ├── 13.4-iot-device-security/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 14-social-engineering-human-factors/     # Domain 14: Social engineering
│   │   ├── 14.1-social-engineering-techniques/
│   │   ├── 14.2-human-psychology-manipulation/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   ├── 15-compliance-governance/                # Domain 15: Compliance & governance
│   │   ├── 15.1-regulatory-frameworks/
│   │   ├── 15.2-security-governance/
│   │   ├── resources/
│   │   ├── tools/
│   │   └── examples/
│   └── 16-career-behavioral-prep/               # Domain 16: Career & behavioral prep
│       ├── 16.1-interview-preparation/
│       ├── 16.2-portfolio-projects/
│       ├── 16.3-communication-leadership/
│       ├── 16.4-career-development/
│       ├── 16.5-scenario-based-interview-walkthroughs/
│       ├── resources/
│       ├── tools/
│       └── examples/
├── docs/                                        # Central coordination hub
│   ├── context.md                               # LLM interaction guidelines (this file)
│   ├── _progress.md                             # Progress tracking across all domains
│   └── _resources.md                            # Global resources and references
```