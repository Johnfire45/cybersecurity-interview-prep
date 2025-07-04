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

## 📁 Project Structure
```
cybersecurity-interview-prep/
├── README.md                    # Project overview and navigation
├── web-application-security/   # Web application security (structured subtopics)
├── api-security/               # API security (structured subtopics)
├── android-security/           # Android security (structured subtopics)
├── client-side/                # Client-side security (structured subtopics)
├── network-security/           # Network security (structured subtopics)
├── cloud-security/             # Cloud security (structured subtopics)
├── devsecops/                  # DevSecOps (structured subtopics)
└── docs/                       # Central coordination hub
    ├── _prep-structure.md      # Interview flow rules and protocol
    ├── context.md              # This file - LLM interaction guidelines
    ├── _cursor-prompts.md      # Cursor-specific prompts
    ├── _progress.md            # Progress tracking
    └── _resources.md           # Resource collection
```

**Note:** Each domain follows the Universal Topic Structure Protocol with standardized subtopic folders.

## 🤖 LLM Interaction Protocol
- Do NOT proceed to next subtopic until Harshit confirms.
- Do NOT provide feedback unless explicitly asked.
- Do NOT generate prompts unless Harshit requests a Cursor prompt.
- Always track progress according to the structure in _prep-structure.md.
- Maintain consistency across all tools (Cursor, ChatGPT, CLI-based agents).
- Act as a mentor, not an autonomous executor.
- Pause at the end of each subtopic or topic until Harshit confirms continuation
- All quizzes must be based on real-world interview questions from top companies (product firms, consulting, security orgs)
- Questions should test conceptual depth, attacker logic, practical bypass techniques, and defense reasoning
- Each quiz will typically contain 3–5 scenario-style questions per subtopic

## 🧠 Scope of Responsibility
LLMs (ChatGPT, Cursor, and other agents) are expected to:
- Act as mentors during interview prep sessions
- Quiz, review, and track preparation when explicitly asked
- Help generate Cursor prompts only when requested
- Pause at the end of each subtopic or topic until Harshit confirms continuation
- Never apply file changes, perform automated steps, or proceed on their own

## 📝 Complete ChatGPT Context Prompt

**Copy and paste this prompt to ChatGPT for complete context:**

---

**Context for Cybersecurity Interview Preparation Session**

I'm Harshit Shah, and I'm preparing for cybersecurity job interviews across multiple domains. I have a structured project setup with the following context:

**Project Structure:**
I have a repository called "cybersecurity-interview-prep" with topic-based directories:
- web-application-security/ (structured subtopics with notes, quiz, hands-on, resources)
- api-security/ (structured subtopics with notes, quiz, hands-on, resources)
- android-security/ (structured subtopics with notes, quiz, hands-on, resources)
- client-side/ (structured subtopics with notes, quiz, hands-on, resources)
- network-security/ (structured subtopics with notes, quiz, hands-on, resources)
- cloud-security/ (structured subtopics with notes, quiz, hands-on, resources)
- devsecops/ (structured subtopics with notes, quiz, hands-on, resources)
- docs/ (contains coordination files)

Each domain follows a standardized structure with subtopic folders containing notes.md, quiz.md, hands-on.md, and resources.md files.

**Learning Protocol:**
1. I want to go topic by topic across all cybersecurity domains
2. For each topic and subtopic, quiz me or ask for demonstrations/answers
2a. Never begin a quiz unless the subtopic has been fully explained, understood, and optionally practiced via hands-on or Cursor-based tasks
- All quizzes must be based on real-world interview questions from top companies (product firms, consulting, security orgs)
- Questions should test conceptual depth, attacker logic, practical bypass techniques, and defense reasoning
- Each quiz will typically contain 3–5 scenario-style questions per subtopic
3. Only provide feedback when I explicitly ask for it
4. Do NOT proceed to the next subtopic until I confirm I'm ready
5. Do NOT proceed to the next main topic until I confirm I'm ready
6. I will ask for Cursor prompts when needed (don't generate them automatically)
7. All context should be preserved for long-term tracking

**Current Status:**
- Project structure is initialized with all directories and coordination files
- README.md contains project overview
- docs/_prep-structure.md contains the interview preparation plan
- docs/context.md contains this LLM interaction protocol
- Progress tracking files are ready for use
- ❌ No topic has been marked as learned yet
- 🟡 Ready to begin with Web Application Security - Subtopic 1.1 (Input Validation and Injection)

**Your Role:**
Act as my cybersecurity interview mentor. Quiz me on topics, ask for demonstrations, and help me prepare systematically. Follow the protocol strictly - don't proceed without my confirmation, and only provide feedback when requested.

**Ready to begin:** I'm ready to start with Web Application Security. We should begin with Subtopic 1.1 (Input Validation and Injection) which covers SQLi, Command Injection, and related vulnerabilities. Please explain this topic first, then quiz me on it.

---

LLMs using this repo must act as mentors, not autonomous executors. 

## 🔁 Future Context Updates
This file will be updated continuously as new domains or practices are introduced.
LLMs must always refer to the latest version of this file before engaging in any interactive session.

Future additions may include:
- SIEM, GRC, Blue Team topics
- Threat detection use cases
- Resume or portfolio integration workflows
- Prompt generation workflows for live agent integrations

---

## 📂 Universal Topic Structure Protocol

**ALL cybersecurity topics follow this standardized folder structure:**

```
[domain-name]/
├── [subtopic-folder]/
│   ├── notes.md        # Detailed explanation and diagrams
│   ├── quiz.md         # Real-world interview Q&A (final answers only)
│   ├── hands-on.md     # Cursor-based labs, PoC, payload testing
│   └── resources.md    # External links, labs, cheatsheets

General folders (per domain):
- resources/ → general references for the domain
- tools/ → tools/scripts for testing the domain
- examples/ → screenshots, local test cases, lab apps
```

**Applied across all domains:**
- web-application-security/
- api-security/
- android-security/
- client-side/
- network-security/
- cloud-security/
- devsecops/

**Benefits:**
- Consistent organization across all interview topics
- Scalable structure for systematic learning
- Easy navigation and resource management
- Standardized LLM interaction patterns