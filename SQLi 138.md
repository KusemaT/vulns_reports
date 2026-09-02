## Title

SQL Injection via email Parameter on login Page

## Vulnerability Type

SQL Injection

## Summary

Moviexchange https://kzlabs.in/subdomains/login/index.php?view=login email parameter is vulnerable to SQL injection.
The application fails to properly sanitise/parameterise user input from the email parameter before including it in a SQL query, allowing an attacker to inject arbitrary SQL syntax. This can be used to bypass authentication, extract data from the database (including other users' data or sensitive tables), modify/delete data, or in some cases achieve remote code execution on the underlying server.

## Vulnerable Endpoint

`https://kzlabs.in/subdomains/login/index.php?view=login`

## Steps to Reproduce

1. Navigate to the following URL,: `https://kzlabs.in/subdomains/login/index.php?view=login`
2. Capture Request in Burpsuite
3. Manipulate email parameter with payloads in intruder
4. Use the injection point to confirm data extraction, e.g. via `UNION SELECT` or a boolean/time-based technique, and enumerate database name, tables, and columns

## Payload used

`if(1=1,sleep(10),0)/'XOR(if(1=1,sleep(10),0))OR'"XOR(if(1=1,sleep(10),0))OR"/`

(Other useful payloads to demonstrate impact: `' UNION SELECT null,null,null-- -` to determine column count, `' UNION SELECT username,password,null FROM users-- -` to extract data, `' AND SLEEP(5)-- -` for time-based blind confirmation, `'` alone to trigger a database error)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload submitted in vulnerable [field/parameter name]`

<img width="1528" height="1132" alt="image" src="https://github.com/user-attachments/assets/7b23faa0-9542-45e2-845a-9a6ff5ae4436" />


`Screenshot 2: Screenshot shows the resulting database time delay`

<img width="1782" height="1045" alt="image" src="https://github.com/user-attachments/assets/7c739a74-f2b7-4fae-92f4-da9c9f4ffcf9" />


## Impact

An attacker can perform the following actions using this vulnerability:

- It allows attackers to bypass authentication and log in as any user without valid credentials
- It allows attackers to read sensitive data from the database, including other users' credentials and personal information
- It allows attackers to modify or delete data within the database, affecting data integrity
- Depending on database privileges and configuration, it may allow attackers to read/write files on the underlying server or achieve remote code execution
- It can result in full compromise of the backend database and, by extension, the application

## Recommendations for fix

Ensure all user-supplied input is safely handled before being used in SQL queries. This can be done by:

1. Use parameterised queries / prepared statements (or a properly configured ORM) for all database access — never concatenate user input directly into SQL strings
2. Apply strict server-side input validation (e.g. allow-list expected format/type, such as numeric-only for an ID parameter)
3. Enforce the principle of least privilege for the database account used by the application (no unnecessary `DROP`, `FILE`, or admin privileges)
4. Disable detailed database error messages in production to avoid leaking schema information
5. Use a WAF (e.g. Cloudflare) as a defence-in-depth measure to catch common injection payloads before they reach the application
