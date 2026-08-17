## Title

 Reflected Cross-Site Scripting (XSS) Open Redirect via returnTo Parameter in Account Confirmation End

## Vulnerability Type

Reflected XSS

## Summary  
The returnTo parameter is vulnerable and Open Redirect due to improper input validation. An attacker can inject a javascript: URI or external URL, leading to JavaScript execution or redirection to a malicious website when a user clicks the Continue button.

## Vulnerable Endpoint
https://labs.krazeplanet.com/57.php?returnTo=

Steps to Reproduce 

1. Navigate to the following URL : ```https://kzlabs.in/57.php?returnTo=tadz%27%22%3E%3CscRIPT%3Eprompt(1)%3C/scRIPT%3E```
2. Observe that a JavaScript prompt box pops up displaying `1` — confirming that the script executed.

## Payload Used

```tadz'"><scRIPT>prompt(1)</scRIPT>```


## Proof of Concept Request
  ```Screenshot 1 : ``` Screenshot Shows Payload add in URL parameter

  <img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/6760cfb6-61c1-44ff-8743-15dd05f4db1b" />


 ```Screenshot 2 : ``` Screenshot Shows Successfully the Payload has worked with prompt box

 <img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/447600ba-d42b-4489-967f-601e86022894" />

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
