## Title
 Stored Cross-Site Scripting (XSS) via Report Name Field in Network Reports

## Vulnerability Type

Stored XSS

## Summary  
The "Report Name" input field in the Network Reports section does not sanitize or encode user-supplied input before storing it in the database and later rendering it back to users. This means any JavaScript payload entered as a report name gets saved and then executed in the browser of every authenticated user who visits the Reports page — not just the attacker.

## Vulnerable Endpoint
http://kzlabs.com/60.php

Vulnerable Parameter: Report Name field (input field inside the "New Network Report" form)

## Steps to Reproduce  

1. Log in to the application at `https://kzlabs.com/60.php` with a valid account.
2. Navigate to the Reports tab.
3. Click on + New Network Report.
4. In the Report Name field, enter the following payload: `tadz1'"><scRipt>prompt(2)</scRipt>`
5. Fill in the remaining required fields (Network, Date Range, etc.) and submit the form.
6. Once the report is saved, you are redirected back to the Network Reports listing page.
7. Observe that a JavaScript alert box pops up displaying `1` — confirming that the script executed.
8. Every authenticated user who loads this page will trigger the same alert.

## Payload Used

```tadz1'"><scRipt>confirm(1)</scRipt>```

## Proof of Concept Request
  ```Screenshot 1``` The Reports listing page showing the raw script payload stored as-is in row #8 under "Created By" as `tadz1'"><scRipt>confirm(1)</scRipt>`, meaning the app saved it exactly as typed with no filtering at all.
 
<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/ad8b46a9-ac20-4ef1-bf30-6cc0b9ed8e68" />

```Screenshot 2 : ``` Screenshot showing payload executing successfully as prompt box when page loads

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/2eb1e967-bba3-40a4-be43-d3174e46912b" />

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
