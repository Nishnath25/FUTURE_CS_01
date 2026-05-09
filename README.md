# 🔐 Vulnerability Assessment Report — Altoro Mutual

> **Task 1 | Cyber Security Internship | Future Interns**
> Web Application Security Assessment performed on a deliberately vulnerable demo banking site for educational purposes.

---

## 🌐 Website Tested

| Field | Details |
|---|---|
| **Target** | Altoro Mutual Online Banking |
| **URL** | http://demo.testfire.net |
| **Type** | Deliberately vulnerable demo web application |
| **Published by** | HCL Technologies Ltd. (for security testing education) |
| **Date of Assessment** | 9 May 2026 |

---

## 🎯 Scope

- **In scope:** Application-layer vulnerabilities (login, search, feedback, HTTP headers, cookies)
- **Out of scope:** Network infrastructure, third-party services, denial-of-service attacks
- **Method:** Passive and read-only analysis — no data was modified or destroyed
- **Approach:** Followed OWASP Testing Guide methodology

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **OWASP ZAP 2.17.0** | Automated passive vulnerability scanning |
| **Browser DevTools (Chrome)** | HTTP response header inspection |
| **Web Browser** | Manual testing of login, search, and feedback forms |

---

## 📊 Summary of Findings

### Automated Scan Results (OWASP ZAP)

| Risk Level | Number of Alerts |
|---|---|
| 🔴 High | 3 |
| 🟠 Medium | 5 |
| 🟡 Low | 9 |
| 🔵 Informational | 5 |
| **Total ZAP Alerts** | **22** |

### Additional Manual Testing Findings

| Finding | Method |
|---|---|
| Default credentials accepted (admin/admin) | Manual login test |
| Verbose error message on failed login | Manual login test |

---

## 🔴 High Risk Findings (ZAP Confirmed)

### 1. Reflected Cross-Site Scripting (XSS)
- **Location:** `search.jsp`, `sendFeedback` (parameter: name)
- **CWE ID:** 79 | **WASC ID:** 8
- **Evidence:** Injecting `<script>alert('xss')</script>` triggered a browser alert popup. Also confirmed by ZAP active scan.
- **Impact:** Attackers can steal session cookies, redirect users to malicious sites, or perform actions on a victim's behalf
- **Fix:** Encode all user-supplied output using HTML escaping; implement a Content-Security-Policy (CSP) header

### 2. SQL Injection
- **Location:** `doLogin` — login form (parameter: `uid`)
- **CWE ID:** 89 | **WASC ID:** 19
- **Evidence:** ZAP confirmed with payload `ZAP' OR '1'='1' --`
- **Impact:** Attacker can bypass authentication or extract, modify, or delete the entire database
- **Fix:** Use parameterized queries (prepared statements) for all database interactions; never concatenate raw input into SQL strings

### 3. PII Disclosure
- **Location:** Application HTTP responses
- **CWE ID:** 359 | **WASC ID:** 13
- **Evidence:** ZAP detected responses containing Personally Identifiable Information such as credit card numbers and SSN-like data
- **Impact:** Sensitive customer financial data exposed to unauthorized parties — critical for a banking application
- **Fix:** Remove or mask all sensitive data from HTTP responses; implement strict data minimization policies

---

## 🟠 Medium Risk Findings (ZAP Confirmed)

### 4. Content Security Policy (CSP) Header Not Set
- **Location:** All pages
- **Impact:** Browser has no instructions on which sources are trusted, enabling XSS and data injection attacks
- **Fix:** Add `Content-Security-Policy` header in web server configuration

### 5. Absence of Anti-CSRF Tokens
- **Location:** All forms across the application
- **Impact:** Attackers can trick logged-in users into submitting unintended requests (e.g., transferring funds)
- **Fix:** Add a unique, unpredictable CSRF token to every state-changing form

### 6. Missing Anti-Clickjacking Header
- **Location:** All pages
- **Impact:** Site can be embedded in a hidden iframe to trick users into clicking malicious elements
- **Fix:** Add `X-Frame-Options: DENY` or use CSP `frame-ancestors 'none'`

### 7. Cross-Domain Misconfiguration
- **Location:** Application-wide
- **Impact:** Allows unauthorized cross-origin access to application resources
- **Fix:** Configure CORS policy to allow only explicitly trusted domains

### 8. Cookie Without SameSite Attribute
- **Location:** `JSESSIONID` session cookie
- **Impact:** Session cookie can be sent in cross-site requests, enabling CSRF attacks
- **Fix:** Set `SameSite=Strict` or `SameSite=Lax` on all cookies

---

## 🟡 Low Risk Findings (ZAP + Manual)

### 9. No HTTPS / TLS Encryption
- **Location:** Entire site (HTTP only)
- **Evidence:** Browser shows "Not secure" warning; Request URL is `http://`
- **Impact:** All data including login credentials transmitted unencrypted over the network
- **Fix:** Install a free TLS certificate (Let's Encrypt) and redirect all HTTP to HTTPS

### 10. Server Version Disclosure
- **Location:** HTTP response header — `Server: Apache-Coyote/1.1`
- **Impact:** Reveals exact server software and version; attackers can look up known CVEs for that version
- **Fix:** Set `ServerTokens Prod` in Apache config to suppress version information

### 11. Default Credentials Accepted *(Manual Finding)*
- **Location:** `login.jsp`
- **Evidence:** Successfully logged in with `admin` / `admin` — reached "Hello John Smith" dashboard
- **Impact:** Any attacker can access the full banking dashboard with publicly known credentials
- **Fix:** Disable default credentials; enforce strong password policy; force password change on first login

### 12. Verbose Login Error Message *(Manual Finding)*
- **Location:** `login.jsp`
- **Evidence:** Error says "this username or password was not found" — reveals whether a username exists
- **Impact:** Helps attackers enumerate valid usernames (username harvesting)
- **Fix:** Replace with a generic message: "Invalid username or password"

---


---

## ⚠️ Disclaimer

This assessment was conducted **solely for educational and internship purposes**.

- The target (`demo.testfire.net`) is a **deliberately vulnerable demo application** published by HCL Technologies Ltd. for security training
- **No real users, real data, or production systems were affected**
- All testing was **read-only and non-intrusive** — no data was modified, deleted, or exfiltrated
- This report is intended to demonstrate vulnerability assessment skills as part of the Future Interns Cyber Security Internship program

---

## 👤 Author

**Nishanth N Naik**
Cyber Security Intern — Future Interns
Task 1: Vulnerability Assessment Report for a Live Website
May 2026
