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


---

### 🧩 Question 2:
**Scenario:**  
You're asked to improve security in a legacy web app vulnerable to both XSS and SQLi. The dev team says they already use basic regex filters to block certain characters.

#### ❓ Questions:
1. Why is encoding as important as input validation?
2. How would you apply context-aware encoding in:
   - HTML body
   - JavaScript context (inline)
   - SQL queries
3. What libraries/tools would you use?

✅ **Final Answer:**

1. **Why encoding is critical:**  
   Input validation attempts to block malicious patterns at entry points but cannot cover every possible payload or bypass technique. Encoding ensures that any untrusted data that reaches the output layer is treated as inert content rather than executable code. It's the last line of defense that neutralizes potentially dangerous characters by converting them into safe equivalents.

2. **Context-aware encoding examples:**
   - **HTML Body:** Encode characters like `<`, `>`, `&`, `"` using HTML entities (e.g., `&lt;`, `&gt;`, `&quot;`) to prevent injection into visible HTML.
   - **JavaScript Context:** Encode `"` as `\x22`, `'` as `\x27`, and escape newlines, backslashes, etc., to prevent breaking out of strings or executing scripts.
   - **SQL Queries:** Use parameterized queries (e.g., `?` placeholders in SQL libraries) rather than trying to encode input manually, which is error-prone.

3. **Recommended Libraries/Tools:**
   - **HTML/JS Encoding:** OWASP Java Encoder (Java), Microsoft AntiXSS (C#), `html-escape` in Python, or templating engines like React/Vue (auto-escape).
   - **SQL Injection Prevention:** Use parameterized queries in libraries like `pg` (PostgreSQL for Node.js), SQLAlchemy (Python), Hibernate (Java), etc.

---

### 🧩 Question 3:
**Scenario:**  
You’re testing a payment endpoint that expects this JSON:
```json
{
  "amount": 100,
  "userId": "user_123"
}
```
The backend is written in a dynamically typed language like Python or JavaScript.

#### ❓ Questions:
1. What types of validation should be enforced on each field?
2. What kinds of attacks could occur if this validation is missing?
3. How would you structure validation logic in a secure backend?

✅ **Final Answer:**

1. **Field-Level Validation:**

   - `amount`:
     - Must be a positive number (no negative or zero)
     - Type check: integer or float
     - Max cap to prevent overflow (e.g., 100000)
   - `userId`:
     - Must match regex like `^user_\d+$`
     - Length-limited (e.g., ≤32 characters)
     - Only alphanumerics and underscore allowed

2. **Possible Attacks Without Validation:**
   - **Amount tampering:** Sending `-100` or `0.01` through MITM tools like Burp Suite to exploit pricing logic
   - **SQL Injection via userId:** e.g., `user_123' OR '1'='1` if string concatenation is used
   - **Type confusion:** Passing objects or booleans instead of expected primitives (e.g., `userId: { "$gt": "" }`)

3. **Secure Backend Validation Workflow:**
   - ✅ **Schema Validation Layer:** Use tools like Joi, zod, or Pydantic to validate input shape, types, and patterns
   - ✅ **Business Logic Validation:** Check price boundaries, enforce discounts on backend only, verify user role
   - ✅ **Vulnerability Guardrails:** Use parameterized queries, avoid string-based DB access, sanitize logs

   Example (Node.js with pg):
   ```js
   const query = 'SELECT * FROM users WHERE userId = $1';
   const values = [req.body.userId];
   db.query(query, values);
   ```
