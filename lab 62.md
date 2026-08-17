## Title

 Stored XSS in Profile Signature Field

## Vulnerability Type

Stored XSS

## Summary 
The "Signature" input field in the Profile section does not sanitize or encode user-supplied input before storing it in the database and later rendering it back to users. This means any JavaScript payload entered as a report name gets saved and then executed in the browser of every authenticated user who visits the Profile page — not just the attacker.

## Vulnerable Endpoint
http://kzlabs.com/62.php

Vulnerable Parameter: Report Name field (input field inside the "New Network Report" form)

## Steps to Reproduce 

1. Log in to the application at `https://kzlabs.com/62.php` with a valid account.
2. Navigate to the Profile page..
3. In the Profile Signature field, enter the following payload: `'''><iMg src=x oneRRor=alert(150)>`
5. Fill in the remaining required fields (Display,About me etc) and Save the profile.
6. Once the Profile  is saved
7. Observe that a JavaScript alert box pops up displaying `1` — confirming that the script executed.
8. Every authenticated user who loads this page will trigger the same alert.

## Payload Used

```'''><iMg src=x oneRRor=alert(150)>```

## Proof of Concept Request

  ```Screenshot 1``` Screenshot shows payload in vulnerable parameter of profile signature

  <img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/75c4300d-5d86-453b-8502-0709e1d78e51" />

 ```Screenshot 2 : ``` Used an img tag payload `'''><iMg src=x oneRRor=alert(150)>` this time and the alert still triggered on page load

 <img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/7a611e9b-f2c0-4f42-a5f4-037d8ab1c9b1" />

## Impact
 
 An attacker can perform the following actions using this vulnerability:
 
- Steal session cookies of every user who visits the page
- Admins are affected too since they visit the same page
- Can inject fake login forms to harvest credentials
- One submission hits every authenticated user
  
## Recommendations for fix

 Validate and sanitize the redirectUrl parameter to ensure that it does not contain any malicious content. This can be done by:

1. Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
2. Filter out JavaScript methods like: `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
