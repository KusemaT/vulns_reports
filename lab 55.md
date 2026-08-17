## Title

 Reflected Cross-Site Scripting (XSS) via search Parameter on Help Center Page

 ## Vulnerability Type

 Reflected XSS

 ## Summary
 Equifax Help Center http://kzlabs.com/55.php?search= 
 Search parameter is vulnerable to reflected XSS
 The website reflects the user input from search parameter which attacker can inject malicious javascript codeine the victims browser when victim visits malicious URL.

## Vulnerable Endpoint
```https://kzlabs.in/55.php?search=tadz%3C%2Fscript%3E%3Cscript%3Ealert%281%29%3C%2Fscript%3E#```

## Steps to Reproduce 
1. Navigate to the following URL: ```https://kzlabs.in/55.php?search=tadz%3C%2Fscript%3E%3Cscript%3Ealert%281%29%3C%2Fscript%3E#```
2. Javascript alert box pops up when page loads displaying "1" - confirms that script has executed

## Payload used

```tadz</script><script>alert(1)</script>```

## Proof of Concept Request
```Screenshot 1: Screenshot shows payload in vulnerable search bar```

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/ea4fa137-dd0d-4d9c-913a-7f8213ab8956" />

```Screenshot 2: Screenshot shows the execution of the payload as alert popup```

<img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/c3fde8a0-df5e-45bc-addc-1a0b90a18ee8" />

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
