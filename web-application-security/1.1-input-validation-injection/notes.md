


# 📌 1.1 Input Validation & Injection

## 🔍 What is Input Validation?
Input validation is the process of ensuring user-provided input is clean, correct, and safe before it's processed by the application. Proper validation prevents malicious data from entering the system and triggering unintended behaviors.

---

## 🚨 Why It Matters
Failing to validate input properly can lead to:
- SQL Injection
- Command Injection
- Cross-Site Scripting (XSS)
- Path Traversal
- Local/Remote File Inclusion
- Buffer Overflow

---

## 🧪 Types of Validation

| Type | Description |
|------|-------------|
| **Whitelist** | Only allow defined, expected values (most secure) |
| **Blacklist** | Disallow known bad values (bypassable) |
| **Length checks** | Enforce size constraints |
| **Format checks** | Validate pattern using regex (e.g., email, phone) |
| **Type checks** | Enforce input types (e.g., number, string, boolean) |
| **Encoding checks** | Ensure no untrusted control characters are accepted |

---

## 🔐 Security Best Practices

- Always validate on the **server side**
- Use built-in validation libraries
- Normalize input before validation (trim, decode)
- Don’t rely on client-side validation for security
- Apply contextual output encoding (e.g., HTML, JS, SQL)

---

## 📉 Injection Attacks Overview

Injection occurs when untrusted input is interpreted as code or commands by the application. Examples:

### 🛠 SQL Injection
```sql
SELECT * FROM users WHERE username = '$user_input'
```
If `$user_input = ' OR 1=1 --`, the query becomes:
```sql
SELECT * FROM users WHERE username = '' OR 1=1 --'
```

### 🛠 Command Injection
```bash
ping $host
```
If `$host = 8.8.8.8; rm -rf /`, it triggers a shell injection.

---

## 🧰 Recommended Defenses

- Use parameterized queries (prepared statements)
- Avoid shell command concatenation with user input
- Validate input strictly against expected values
- Employ Web Application Firewalls (WAFs) as a defense-in-depth

---

## 🧠 Key Takeaways

- Treat all user input as untrusted
- Validate early, encode later
- Combine validation + context-aware encoding
- Prevent injection by avoiding unsafe interpreters
