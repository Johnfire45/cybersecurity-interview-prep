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
- Web Application Security: 🟡 ready to begin from Subtopic 1.1 (Input Validation & Injection)
- Other domains: ❌ not started