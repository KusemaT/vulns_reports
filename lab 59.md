## Title

 Reflected Cross-Site Scripting (XSS) via Path Parameter Injection in Comments Endpoint

## Vulnerability Type

Reflected XSS

## Summary  
The application is vulnerable to Reflected XSS through the POST_ID path parameter.
User input is reflected unsanitized inside an unquoted HTML attribute, which allows an attacker to inject a new event handler attribute and execute malicious JavaScript when a victim hovers over the affected element.

## Vulnerable Endpoint
```http://kzlabs.com/59.php/svc/shreddit/api/comments/askreddit/t3_u9po1l/t1_i5sxroa```

## Steps to Reproduce 
1. Navigate to the following URL : ```https://kzlabs.in/59.php/svc/shreddit/api/comments/askreddit/t3_u9po1l%20onmouseover%3Dalert%28document.domain%29%20y%3D/t1_i5sxroa```
2. Observe that a JavaScript alert box pops up displaying `kzlabs.in` after hovering over "see more comments"

## Payload Used

```t3_u9po1l onfocus=alert(document.domain) autofocus tabindex=1 y=```

## Proof of Concept Request
  ```Screenshot 1 : ``` Screenshot Shows Payload add in search bar 
  <img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/d8fde7c7-8be2-43ae-abda-a46b25e1c6c6" />

```Screenshot 2 : ``` Screenshot Shows the Payload has worked and Pop up Box got Fired.
<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/ca5ec348-44e3-4b89-8d91-c105a07b2bfb" />

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
