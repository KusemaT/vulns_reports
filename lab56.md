## Title

 Reflected Cross-Site Scripting (XSS) via "search posts"  Parameter on Pubg-Community Feed

## Vulnerability Type

Reflected XSS

## Summary  
Pubg-Community Feed  http://kzlabs.com/56.php?p= Search box is vulnerable to reflected XSS 
Website reflects the User input from search posts parameter 
Which leads attacker to add malicious javascript code within victims browser when user visit malicious url . 

## Vulnerable Endpoint
https://labs.krazeplanet.com/56.php?p=

Steps to Reproduce  
 
1. Navigate to the following URL : ```https://kzlabs.in/56.php?p=tadz%27%22%3E%3Cscript%3Eprompt%281%29%3C%2Fscript%3E#```
2. Observe that a JavaScript prompt box pops up displaying `1` — confirming that the script executed.

## Payload Used

```tadz'"><script>prompt(1)</script>```

## Proof of Concept Request
 ```Screenshot 1 : ``` Screenshot  Shows Payload in search bar
 
<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/64b2ac90-d7c7-441e-8a3d-2367abea6416" />

```Screenshot 2 :``` Screenshot shows the payload being executed successfully as prompt pop up is fired

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/956cbf29-798b-4db2-acef-fd92afb2b3c7" />

## Impact
 
 An attacker can perform the following actions using this vulnerability:
  - It allows attackers to hijack user session
  - It potentially leads to full account takeover
  - It allows to perform  unauthorized actions within the vulnerable application
  - It allows attacker to exfiltrate sensitive data
  
## Recommendations for fix

 Validate and sanitize the redirectUrl parameter to ensure that it does not contain any malicious content. This can be done by:

1. Filter out HTML tags like: `<script>`, `<img>`, `<svg>` from the Report Name field before saving anything to the database
2. Filter out JavaScript methods like: `alert()`, `confirm()`, `prompt()` so even if a tag slips through the method won't execute
4. If you're using PHP then use `htmlspecialchars()` function before rendering any user input back to the page
5. Use Cloudflare as they have so many WAF rules that almost all XSS payloads will be blocked automatically before even reaching the server
