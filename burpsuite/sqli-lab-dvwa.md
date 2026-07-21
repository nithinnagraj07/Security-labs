# Lab: SQL Injection — DVWA (Low Security)

**Date:** 21 July 2026
**Tool(s):** Burp Suite Community Edition, DVWA (Docker), MariaDB
**Objective:** Test the "User ID" input field on DVWA's SQL Injection module for classic authentication/query-bypass SQL injection, at Low security level.

## Methodology

1. Deployed DVWA locally via Docker (`docker run --rm -it -p 80:80 vulnerables/web-dvwa`)
2. Logged in with default credentials (`admin` / `password`)
3. Set DVWA Security Level to **Low**
4. Navigated to the SQL Injection module
5. Submitted a baseline query to confirm normal behavior:
   ```
   User ID: 1
   ```
   Result: returned a single user record.
6. Submitted a crafted payload to test for injection:
   ```
   User ID: 1' OR '1'='1
   ```

## Findings

The payload `1' OR '1'='1` successfully bypassed the intended single-record query and returned **all 5 user records** in the database:

```
ID: 1' OR '1'='1  →  admin admin
ID: 1' OR '1'='1  →  Gordon Brown
ID: 1' OR '1'='1  →  Hack Me
ID: 1' OR '1'='1  →  Pablo Picasso
ID: 1' OR '1'='1  →  Bob Smith
```

**Note on payload precision:** An initial attempt using `1' OR '1'='1'` (with a trailing quote) failed with a MariaDB syntax error, because the extra quote left an unmatched string terminator once combined with the application's own closing quote. Removing the trailing quote resolved this.

## Analysis

DVWA's backend (Low security) concatenates user input directly into the SQL query without sanitization or parameterization, roughly:

```php
$query = "SELECT first_name, last_name FROM users WHERE user_id = '$id'";
```

Injecting `1' OR '1'='1` transforms the query into:

```sql
SELECT first_name, last_name FROM users WHERE user_id = '1' OR '1'='1'
```

Because `'1'='1'` evaluates to `TRUE` for every row, the `OR` clause causes the database to return every record in the table, not just the one matching `user_id = 1`.

**Real-world impact:** In a production system, this class of vulnerability could allow an attacker to dump entire user tables (including credentials), bypass login authentication, or in more advanced cases (UNION-based injection) extract data from other tables entirely.

**Mitigation:** Use parameterized queries / prepared statements (e.g., PDO or mysqli prepared statements in PHP) so user input is always treated as data, never as executable SQL. Input validation and least-privilege database accounts are additional defense layers.

## Key Takeaway

User input that is concatenated directly into a SQL query — instead of being passed as a parameter — allows an attacker to alter the query's logic. Even a single unescaped quote character is enough to break out of the intended data context and inject arbitrary SQL logic.
<img width="903" height="779" alt="Screenshot 2026-07-21 at 8 51 52 PM" src="https://github.com/user-attachments/assets/19c5be1e-993d-47dc-bb13-07605a412374" />
