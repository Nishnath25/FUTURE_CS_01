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
| **CIN ID** | FIT/APR26/CS7733 |
| **Project** | FUTURE_CS_01 |

---

## 🎯 Scope

- **In scope:** Application-layer vulnerabilities (login, search, feedback, HTTP headers, cookies, session management)
- **Out of scope:** Network infrastructure, third-party services, denial-of-service testing
- **Method:** Passive and read-only analysis — no data was modified or destroyed
- **Approach:** Followed OWASP Testing Guide (v4) methodology

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **OWASP ZAP 2.17.0** | Automated passive vulnerability scanning — identified web application vulnerabilities such as missing security headers, CSRF issues, XSS, and SQL injection |
| **Nmap 7.99** | Network scanning — discovered open ports, running services, and network exposure |
| **Browser DevTools (Chrome)** | Inspected HTTP response headers, analyzed cookies, examined network requests |
| **Canva** | Designed and formatted the final professional report |

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

### Network Scan Results (Nmap)

| Port | State | Service |
|---|---|---|
| 80/tcp | Open | HTTP |
| 443/tcp | Open | HTTPS |
| 8080/tcp | Open | HTTP-Proxy |
| 8443/tcp | Closed | HTTPS-Alt |

---

## 🔴 High Risk Findings

### 1. Reflected Cross-Site Scripting (XSS)
- **Location:** `search.jsp`, `sendFeedback` (parameter: name)
- **CWE:** 79 | **WASC:** 8
- **Evidence:** Injecting `<script>alert('xss')</script>` triggered a browser alert popup. Also confirmed by ZAP active scan.
- **Impact:** Attackers can steal session cookies, hijack accounts, or redirect users to malicious sites
- **Fix:** Encode all user output with HTML escaping; implement Content-Security-Policy header

### 2. SQL Injection
- **Location:** `doLogin` — login form (parameter: `uid`)
- **CWE:** 89 | **WASC:** 19
- **Evidence:** ZAP confirmed with payload `ZAP' OR '1'='1' --`
- **Impact:** Attacker can bypass authentication or extract the entire database
- **Fix:** Use parameterized queries; validate and sanitize all inputs

### 3. PII Disclosure
- **Location:** Application HTTP responses
- **CWE:** 359 | **WASC:** 13
- **Evidence:** ZAP detected credit card numbers and SSN-like data in responses
- **Impact:** Sensitive customer financial data exposed — violates GDPR and PCI-DSS
- **Fix:** Mask all sensitive data; implement data minimization policy

### 4. Default Credentials Accepted *(Manual Finding)*
- **Location:** `login.jsp`
- **Evidence:** Successfully logged in with `admin` / `admin` — accessed "Hello John Smith" dashboard
- **Impact:** Any attacker can gain full banking access instantly
- **Fix:** Disable default credentials; enforce MFA and account lockout

---

## 🟠 Medium Risk Findings

### 5. Missing Security Headers
- **Location:** All pages (HTTP response headers)
- **Missing:** Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, Strict-Transport-Security
- **Fix:** Add all security headers in web server configuration

### 6. Absence of Anti-CSRF Tokens
- **Location:** All forms
- **Fix:** Add unique CSRF token to every state-changing form

### 7. Missing Anti-Clickjacking Header
- **Location:** All pages
- **Fix:** Add `X-Frame-Options: DENY`

### 8. Cookie Without SameSite Attribute
- **Location:** `JSESSIONID` session cookie
- **Evidence:** Cookie set without Secure flag or SameSite attribute
- **Fix:** Set `SameSite=Strict` and `Secure` flag on all cookies

---

## 🟡 Low Risk Findings

### 9. No HTTPS / TLS Encryption
- **Location:** Entire site (HTTP only)
- **Evidence:** Browser shows "Not secure" warning
- **Fix:** Install free TLS certificate via Let's Encrypt; redirect all HTTP to HTTPS

### 10. Server Version Disclosure
- **Location:** HTTP response header — `Server: Apache-Coyote/1.1`
- **Fix:** Set `ServerTokens Prod` in Apache config

### 11. Verbose Login Error Message *(Manual Finding)*
- **Location:** `login.jsp`
- **Evidence:** Error reveals whether username exists in the system
- **Fix:** Use generic message: "Invalid username or password"

---

## 🌐 Network Scan Summary (Nmap)

```
nmap demo.testfire.net

PORT      STATE   SERVICE
80/tcp    open    http
443/tcp   open    https
8080/tcp  open    http-proxy
8443/tcp  closed  https-alt

996 filtered tcp ports (no-response)
Host: demo.testfire.net (65.61.137.117)
```

**Findings:** Open ports increase the external attack surface. Port 8080 may expose additional web services. Apache Tomcat (Coyote JSP Engine) may be vulnerable to known exploits if unpatched.

**Recommendations:**
- Restrict unnecessary open ports using firewall rules
- Disable unused services and administrative interfaces
- Enforce HTTPS and redirect all HTTP traffic securely
- Regularly update and patch Apache Tomcat

---



## ⚠️ Disclaimer

This assessment was conducted **solely for educational and internship purposes**.

- The target (`demo.testfire.net`) is a **deliberately vulnerable demo application** published by HCL Technologies Ltd. for security training
- **No real users, real data, or production systems were affected**
- All testing was **read-only and non-intrusive** — no data was modified, deleted, or exfiltrated
- This report is submitted as part of the Future Interns Cyber Security Internship program

---

## 👤 Author

**Nishanth N Naik**
Cyber Security Intern — Future Interns
CIN ID: FIT/APR26/CS7733
Task 1: Vulnerability Assessment Report for a Live Website
May 2026
