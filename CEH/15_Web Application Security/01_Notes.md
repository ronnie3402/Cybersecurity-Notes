# Exploiting Web Applications (Module 15)

A comprehensive guide to identifying and exploiting web application vulnerabilities using offensive security techniques.




---

## 1. Web Application Architecture

Understanding the architecture of a web app is the first step in attacking (or defending) it — every layer represents a potential entry point.

| Layer | Examples | Role |
|---|---|---|
| **Client Layer** | Browser / mobile app | The interface where users interact with the application |
| **Web Server** | Apache, Nginx, IIS | Handles incoming HTTP requests and sends back responses |
| **Application Logic** | PHP, Java, Python, Node.js | Processes business logic; exposes REST/JSON endpoints for data exchange |
| **Database / APIs** | MySQL, PostgreSQL, MSSQL | Stores and retrieves the application's actual data |

**Simple way to picture it:** the client is the customer, the web server is the front desk, the application logic is the back-office staff actually doing the work, and the database is the filing room where all the records live. An attacker doesn't need to break into the filing room directly — tricking the front desk or the back-office staff into handing over (or modifying) records is usually far easier.

---

## 2. Common Entry Points & Critical Threats

### Attack Entry Points

Attackers target whichever input vector gives them the most direct path into the application's logic:

| Entry Point | Description |
|---|---|
| **URL Parameters** | Query strings and path variables |
| **Form Inputs** | User-submitted data fields |
| **Cookies & Headers** | HTTP headers and session data |
| **API Endpoints** | RESTful service interfaces |

### Critical Threats

| Threat | What Goes Wrong |
|---|---|
| **Unvalidated user input** | Opens the door to injection attacks |
| **Improper authentication handling** | Weak credentials go unchecked |
| **Broken authorization logic** | Enables privilege escalation |
| **Insecure file handling** | Permits malicious file uploads |
| **Poor session management** | Exposes active user sessions |
| **Insecure API endpoints** | Leaks sensitive data |

**The underlying pattern:** almost every vulnerability on this list traces back to one root cause — the application trusting input it should have validated, or trusting a request it should have re-checked for authorization.

---

## 3. Cross-Site Scripting (XSS)

XSS happens when an attacker manages to inject a malicious script into a trusted website, so that the script executes inside a victim's browser — under that victim's own session and permissions.

| Type | How It Works |
|---|---|
| **Reflected XSS** | The malicious script bounces straight back off the server — delivered via a URL or form, and executed immediately, once, for that single request |
| **Stored XSS** | The script is permanently saved on the target server (e.g., in a database, forum post, or comment field), and executes every single time *any* user views the infected page |
| **DOM-based XSS** | The vulnerability lives entirely in client-side code — JavaScript modifies the page's DOM in a way that triggers malicious script execution, without the payload ever necessarily touching the server |

**Why the distinction matters:** Stored XSS is generally considered the most dangerous of the three, since it silently affects every visitor to the infected page rather than requiring an attacker to trick one specific victim into clicking a crafted link (as Reflected XSS does).

**Testing Tools:** Burp Suite (Repeater, Intruder), OWASP ZAP, and browser Developer Tools — used for injecting payloads and inspecting how the DOM responds.

---

## 4. Path Traversal & Command Injection

### Directory (Path) Traversal

Allows an attacker to access files outside the directory the application intended to expose, by manipulating file path references in a request.

**Example payloads:**

```
../../../../etc/passwd
..%2f..%2f..%2fetc%2fpasswd
```

(The second example is the URL-encoded form of `../`, used to slip past basic input filters that only check for the literal `../` string.)

**Testing approach:** inject path traversal sequences into file parameters, e.g. via `curl`.

### Command Injection

Occurs when an application passes unvalidated user input directly into a system command, letting an attacker append their own commands.

**Common payloads:**

```
; whoami
&& id
uname -a
```

The shell operators (`;`, `&&`) chain an extra, attacker-controlled command onto whatever command the application was already going to run.

**Testing approach:** inject shell operators via URL parameters, using tools like Burp Suite or Commix.

---

## 5. File Upload & SSRF Vulnerabilities

### File Upload Vulnerabilities

When file validation is improperly implemented, an attacker can upload an executable file (commonly a **web shell**) that grants remote code execution on the server.

**Typical attack flow:**

1. **Upload an executable file** — a PHP, ASPX, or JSP web shell disguised as or bypassing the expected file type.
2. **Access it via browser** — navigate directly to the uploaded file's location.
3. **Execute commands** — use a `cmd` parameter built into the shell to run arbitrary system commands.

### Server-Side Request Forgery (SSRF)

SSRF tricks the *server itself* into sending requests on the attacker's behalf — often reaching internal resources that would otherwise be unreachable from outside the network.

**Common targets:**

| Target | What It Exposes |
|---|---|
| `http://localhost` | Internal services running on the server itself |
| `http://127.0.0.1:8080` | Specific local ports the outside world can't normally reach |
| `http://169.254.169.254/` | Cloud metadata endpoints (commonly exploited on AWS/Azure/GCP to extract credentials) |

**Testing method:** modify URL parameters in Burp Suite, observe how the server responds, and watch for evidence of internal access attempts.

---

## 6. Authentication & Authorization Flaws

### Broken Authentication

Weak authentication mechanisms — weak passwords, no account lockout policy, or predictable session IDs — allow attackers to gain unauthorized access.

**Testing:** brute-force attacks using Hydra or Burp Intruder, strictly within controlled lab environments.

### Broken Access Control

Missing authorization checks let an attacker reach resources they shouldn't, simply by manipulating a parameter or URL.

**Classic example — IDOR (Insecure Direct Object Reference):**

```
Change:  /user/profile?id=1001
To:      /user/profile?id=1002
```

If the application doesn't verify that the logged-in user actually *owns* `id=1002`, the attacker views someone else's profile data just by editing the number in the URL.

### CSRF Attacks

Cross-Site Request Forgery forces an already-authenticated user's browser to perform an action they never intended, by exploiting the absence of a CSRF token. *(See the earlier Sessions & Cookies notes for the full mechanics of how CSRF works.)*

**Testing:** strip the CSRF token from a request and replay it using Burp Repeater — if the request still succeeds, the protection isn't actually being enforced.

---

## 7. Advanced Web Application Attacks

### Clickjacking

Tricks users into clicking a hidden element by loading the real target page inside an invisible iframe layered beneath a decoy interface — the user thinks they're clicking one thing, but their click actually lands on the hidden page underneath.

**Testing:** check whether the `X-Frame-Options` header is set — its absence means the page can be embedded in an iframe by anyone.

### XML External Entities (XXE)

Exploits insecure XML parsing to read local files or reach internal systems, by abusing custom entity definitions inside an XML document.

**Testing:** craft a malicious `DOCTYPE` declaration containing external entity references and observe how the parser handles it.

### Web API Vulnerabilities

APIs expose application functionality directly through endpoints, which can suffer from broken authentication, excessive data exposure (returning more fields than the client actually needs), or missing rate limiting (allowing abuse at scale).

**Testing:** Postman or Burp Suite, exercising endpoints directly.

### Web Shells

Once uploaded (see Section 5), a PHP, ASPX, or JSP shell gives an attacker ongoing remote control of the compromised server — accessed simply by browsing to the shell's URL and passing commands through a `cmd` parameter.

---

## 8. Web Application Hacking Methodology

A structured, repeatable process security professionals follow to systematically identify and exploit web application vulnerabilities — rather than testing at random.

| Stage | What Happens |
|---|---|
| **1. Reconnaissance & Mapping** | Identify the application's structure, technologies in use, and all reachable endpoints |
| **2. Parameter Identification** | Locate every input vector and trace how data flows through the application |
| **3. Input Manipulation** | Test each injection point with crafted malicious payloads |
| **4. Authentication Testing** | Verify how credentials and session management are actually handled |
| **5. Authorization Testing** | Check access controls and attempt privilege escalation |
| **6. Exploitation** | Execute controlled attacks to confirm and demonstrate impact, strictly within lab environments |
| **7. Documentation** | Record findings with supporting evidence and clear remediation guidance |

**Why order matters here too:** you can't meaningfully test authorization (stage 5) without first knowing how authentication works (stage 4), and you can't responsibly exploit anything (stage 6) without having already mapped out the application and its parameters (stages 1–2).

> This methodology aligns directly with the **OWASP Top 10 Application Security Risks (2025)**, including Injection, Broken Authentication, Broken Access Control, Security Misconfiguration, and Software and Data Integrity Failures.

---

## Key Takeaways / Exam Summary

- Web app architecture has four layers worth knowing: **Client → Web Server → Application Logic → Database/APIs** — each is a distinct attack surface.
- Almost every vulnerability category in this module traces back to one root cause: **the application trusting input or requests it should have validated.**
- The three **XSS** types differ mainly in *where the payload lives and how often it fires*: Reflected (one-time, via URL), Stored (persistent, hits every visitor), DOM-based (entirely client-side).
- **Path Traversal** escapes the intended directory (`../../../etc/passwd`); **Command Injection** chains an extra OS command onto a vulnerable input (`; whoami`).
- **File upload flaws** lead to **web shells**, which then provide ongoing remote command execution.
- **SSRF** turns the server itself into a proxy for the attacker — especially dangerous against cloud metadata endpoints (`169.254.169.254`).
- **IDOR** is the textbook example of Broken Access Control: changing an ID in a URL to access someone else's data.
- **CSRF** abuses an existing session to perform unintended actions; defeating it means actually enforcing CSRF tokens, not just including them.
- **Clickjacking** relies on missing `X-Frame-Options`; **XXE** relies on insecure XML parsing accepting external entities.
- The **methodology** (Recon → Parameters → Input Manipulation → Auth → Authorization → Exploitation → Documentation) is the backbone of structured penetration testing, and maps directly onto the OWASP Top 10.
