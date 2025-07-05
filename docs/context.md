# **LLM Context Version:** v1.0 (2025-07-04)

# LLM Context for Cybersecurity Interview Preparation

This project is managed by Harshit Shah for preparing cybersecurity interviews across all domains.

## 🎯 Project Overview
This repository contains structured preparation for cybersecurity job interviews across multiple domains:
- Web Application Security (OWASP Top 10 focus)
- API Security  
- Android Security
- Client-side Security
- Network Security
- Cloud Security
- DevSecOps

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

- `_progress.md` tracks subtopic-level preparation across domains.
- Statuses: 🟢 Mastered, 🟡 In Progress, 🔴 Not Started, 🧪 Needs Brushing Up.
- Each subtopic includes "Next Up" and "Review Later" annotations.
- Future automation may populate overall stats dynamically.
- Cursor must always discuss before bulk restructuring.

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

- Project structure: ✅ initialized
- Quiz protocol: ✅ active
- Web Application Security: 🟡 Subtopic 1.01 quiz completed, other parts pending
- Other domains: ❌ not started

---

## 📁 Complete Project Directory Structure

```
cybersecurity-interview-prep/
├── README.md                                    # Project overview and navigation
├── .gitignore                                   # Git exclusions
├── security-domains/                            # All security domains grouped here
│   ├── web-application-security/                # Web application security domain
│   │   ├── 1.01-input-validation-injection/     # Subtopic 1.01 🟡 IN PROGRESS (only quiz done)
│   │   │   ├── notes.md                        # 0B - NEEDS CONTENT
│   │   │   ├── quiz.md                         # 1.2KB - HAS CONTENT
│   │   │   ├── hands-on.md                     # 0B - NEEDS CONTENT
│   │   │   └── resources.md                    # 0B - NEEDS CONTENT
│   │   ├── 1.02-cross-site-scripting/          # Subtopic 1.2 ❌ PENDING
│   │   │   ├── notes.md                        # 0B - NEEDS CONTENT
│   │   │   ├── quiz.md                         # 0B - NEEDS CONTENT
│   │   │   ├── hands-on.md                     # 0B - NEEDS CONTENT
│   │   │   └── resources.md                    # 0B - NEEDS CONTENT
│   │   ├── ...
│   │   ├── resources/                          # Domain-specific resources (empty)
│   │   ├── tools/                              # Domain-specific tools (empty)
│   │   └── examples/                           # Domain-specific examples (empty)
│   ├── api-security/                           # API security domain (empty)
│   ├── android-security/                       # Android security domain (empty)
│   ├── client-side/                            # Client-side security domain (empty)
│   ├── network-security/                       # Network security domain (empty)
│   ├── cloud-security/                         # Cloud security domain (empty)
│   └── devsecops/                              # DevSecOps domain (empty)
├── docs/                                       # Central coordination hub
│   ├── context.md                              # LLM interaction guidelines (this file)
│   ├── _progress.md                            # Progress tracking across all domains
│   └── _resources.md                           # Global resources and references
```

**📊 Content Status Summary:**
- **Partially Completed Subtopic:** 1.01 (Input Validation & Injection) - Quiz done, notes/hands-on/resources pending
- **Pending Subtopics:** 1.2 through 1.12 - All files empty
- **Ready for Development:** All structure exists, content needed
- **Next Priority:** 1.2 Cross-Site Scripting (next in sequence)