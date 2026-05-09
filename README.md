**Vulnerability Assessment Report — Altoro Mutual
**
Internship Project | Web Application Security Assessment Performed on a deliberately vulnerable demo banking application for educational purposes.


**🌐 Website Tested**
**Field **                 **Details** 
Target                       Altoro Mutual Online Banking
URL                          http://demo.testfire.net
Type                         Deliberately vulnerable demo web application
Published by                 HCL Technologies Ltd. (for security testing education)
Date  of Assessment          9 May 2026

🎯 Scope

In scope: Application-layer vulnerabilities (login, search, feedback, headers, cookies)
Out of scope: Network infrastructure, third-party services, denial-of-service attacks
Method: Passive and read-only analysis — no data was modified or destroyed
Approach: Followed OWASP Testing Guide methodology


🛠️ Tools Used
Tool                           Purpose
OWASP ZAP 2.17.0               Automated passive vulnerability scanning
Browser DevTools (Chrome)      HTTP response header inspection
Web Browser                    Manual testing of login, search, and feedback forms

📊 Summary of Findings
Risk           LevelCount
🔴 High        4      
🟠 Medium      5
🟡 Low         3
Total          12

🔴 High Risk Findings
1. Reflected Cross-Site Scripting (XSS)

Location: search.jsp, sendFeedback
Evidence: Injecting <script>alert('xss')</script> triggered a browser alert popup
Impact: Attackers can steal session cookies, redirect users, or perform actions on their behalf
Fix: Encode all user output with HTML escaping; add a Content-Security-Policy header

2. SQL Injection

Location: doLogin — login form (uid parameter)
Evidence: ZAP confirmed with payload ZAP' OR '1'='1' -- (CWE-89)
Impact: Attacker can bypass authentication or extract the entire database
Fix: Use parameterized queries (prepared statements); never concatenate raw input into SQL

3. PII Disclosure

Location: Application responses
Evidence: ZAP detected responses containing personally identifiable information such as credit card numbers and SSN-like data (CWE-359)
Impact: Sensitive customer data exposed to unauthorized parties
Fix: Mask or remove sensitive data from all HTTP responses; implement data minimization

4. Default Credentials Accepted

Location: login.jsp
Evidence: Successfully logged in with admin / admin — accessed "Hello John Smith" dashboard
Impact: Any attacker can gain full account access with publicly known credentials
Fix: Force password change on first login; disable default credentials in production


🟠 Medium Risk Findings
5. Missing Security Headers

Location: All pages (HTTP response headers)
Missing: Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, Strict-Transport-Security
Impact: Exposes site to clickjacking, MIME sniffing, and man-in-the-middle attacks
Fix: Add all security headers in the web server configuration

6. Absence of Anti-CSRF Tokens

Location: Forms across the application
Impact: Attackers can trick logged-in users into submitting unintended requests
Fix: Add a unique CSRF token to every state-changing form

7. Cross-Domain Misconfiguration

Location: Application-wide
Impact: Allows unauthorized cross-origin access to resources
Fix: Configure CORS policy to allow only trusted domains

8. Missing Anti-Clickjacking Header

Location: All pages
Impact: Site can be embedded in a hidden iframe to trick users into clicking malicious elements
Fix: Add X-Frame-Options: DENY header

9. Cookie Without SameSite Attribute

Location: JSESSIONID session cookie
Impact: Cookie can be sent in cross-site requests, enabling CSRF attacks
Fix: Set SameSite=Strict or SameSite=Lax on all cookies


🟡 Low Risk Findings
10. No HTTPS / TLS Encryption

Location: Entire site (HTTP only)
Evidence: Browser shows "Not secure" warning in address bar
Impact: All data including login credentials transmitted in plain text
Fix: Install a TLS certificate (e.g., Let's Encrypt — free) and redirect all HTTP to HTTPS

11. Server Version Disclosure

Location: HTTP response header — Server: Apache-Coyote/1.1
Impact: Reveals exact server version; attackers can look up known exploits
Fix: Set ServerTokens Prod in Apache config to hide version details

12. Admin Panel Publicly Discoverable

Location: /admin directory visible in ZAP site tree
Impact: Attackers can target the admin interface directly
Fix: Restrict access to /admin by IP whitelist or VPN only
