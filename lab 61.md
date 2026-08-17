## Title

Stored XSS via Rich Text Editor HTML Tab in Article Body 

## Vulnerability Type

Stored XSS

## Summary  

The application is vulnerable to Stored Cross-Site Scripting (XSS) through the article Body (HTML) field. User-supplied HTML is stored without sanitization and rendered using innerHTML, allowing an attacker to inject malicious JavaScript that executes when users view the affected article page.

## Vulnerable Endpoint
http://kzlabs.com/61.php

Vulnerable Parameter: article Body (HTML) field (input field inside the "Write article" form)

## Steps to Reproduce  

1. Log in to the application at `https://kzlabs.com/61.php` with a valid account.
2. Navigate to the My Article tab.
3. Click on + Write article .
4. In the Body (html) field, enter the following payload: `'''><iMg src=x oneRRor=alert(1)>`
5. Fill in the remaining required fields (Title, Tag, etc.) and submit the form.
6. Once the report is saved, you are redirected back to the article  listing page.
7. Observe that a JavaScript alert box pops up displaying `1` — confirming that the script executed.
8. Every authenticated user who loads this page will trigger the same alert.

## Payload Used

```'''><iMg src=x oneRRor=alert(1)>```

## Proof of Concept Request
  ```Screenshot 1``` Screenshot showing the raw script payload input under body html when adding new article 

<img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/3d271cc6-7bd0-42d1-b6ab-a6c46955e12d" />

 ```Screenshot 2 : ``` Used an img tag payload `'''><iMg src=x oneRRor=alert(1)>` this time and the alert still triggered on page load,

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/a1745cc4-f6b5-4ece-9ba3-a7b3a1577eda" />

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
