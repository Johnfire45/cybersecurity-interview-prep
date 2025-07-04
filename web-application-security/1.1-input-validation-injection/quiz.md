# 🧪 Subtopic Quiz — 1.1.1 Input Validation Fundamentals

This file contains real-world interview questions and finalized answers for this subtopic.

---

### 🧩 Question 1:
**Developer says:**  
"We're validating user input with a regex on the frontend and disallowing angle brackets (`<`, `>`), so XSS isn't a problem."

✅ **Final Answer:**
"This approach is insecure for several reasons:

1. Client-side validation alone is ineffective — it can be bypassed by disabling JS, intercepting requests, or crafting payloads manually.

2. Simple regex filtering based on angle brackets doesn't stop XSS. Attackers can use:
- HTML entities: `&lt;`, `&gt;`
- Alternate tags like `<svg/onload=alert(1)>`
- Event handlers inside allowed tags: `<img src=x onerror=...>`
- JavaScript contexts like `"><script>...</script>`

3. XSS defenses must be context-aware and server-side:
- Use output encoding based on the injection point (HTML, JS, attribute, etc.)
- Implement Content Security Policy (CSP) to restrict inline script execution
- Use frameworks with built-in escaping (React, Django templates, etc.)

Therefore, relying on front-end regex filtering gives a false sense of security and doesn't address the root problem."
