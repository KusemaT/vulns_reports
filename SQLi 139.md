## Title

SQL Injection via Comment Parameter on Dev Community Page

## Vulnerability Type

SQL Injection

## Summary

Butter CMS https://kzlabs.in/subdomains/reviews/ Comment parameter is vulnerable to SQL injection.
The application fails to properly sanitise/parameterise user input from the Comment parameter before including it in a SQL query, allowing an attacker to inject arbitrary SQL syntax. This can be used to bypass authentication, extract data from the database (including other users' data or sensitive tables), modify/delete data, or in some cases achieve remote code execution on the underlying server.

## Vulnerable Endpoint

`https://kzlabs.in/subdomains/reviews/`

## Steps to Reproduce

1. Navigate to the following URL: `https://kzlabs.in/subdomains/reviews/`
2. Capture post comment request in Burpsuite
3. Manipulate comment parameter in intruder and add payloads
4. Use the injection point to confirm data extraction, e.g. via `UNION SELECT` or a boolean/time-based technique, and enumerate database name, tables, and columns

## Payload used

`0'XOR(if(now()=sysdate(),sleep(6*2),0))XOR'Z`

(Other useful payloads to demonstrate impact: `' UNION SELECT null,null,null-- -` to determine column count, `' UNION SELECT username,password,null FROM users-- -` to extract data, `' AND SLEEP(5)-- -` for time-based blind confirmation, `'` alone to trigger a database error)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload submitted in vulnerable [field/parameter name]`

<img width="1653" height="1132" alt="image" src="https://github.com/user-attachments/assets/eaa472d5-322a-4bdb-b229-a14a6935faba" />


`Screenshot 2: Screenshot shows the resulting database error / altered query behaviour / extracted data`

<img width="1541" height="1046" alt="image" src="https://github.com/user-attachments/assets/7514d419-3cd9-4e43-bb1a-ee5fa44bc261" />


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
