
## Title

Open Redirect via logout Parameter on dashboard Page

## Vulnerability Type

Open Redirect

## Summary

Tumblr https://admin-sso.kzlabs.in/ log out parameter is vulnerable to an open redirect.
The website redirects users to an external URL supplied via the logout parameter without validating that the destination belongs to a trusted/allow-listed domain. An attacker can craft a link that appears to point to the legitimate site but silently redirects the victim to an attacker-controlled site, which is commonly abused for phishing and credential theft.

## Vulnerable Endpoint

`https://admin-sso.kzlabs.in/`

## Steps to Reproduce

1. Navigate to the following URL: `https://admin-sso.kzlabs.in/`
2. Capture logout redirect in burp suite
3. Send request to intruder
4. Observe that the browser is redirected away from the trusted domain to the attacker-controlled domain (`evil.example`) without any warning

## Payload used

`bing.com`

(Other useful payloads to demonstrate impact/bypass: `//evil.example`, `https://target.example.evil.example`, `https:/evil.example`, `/\evil.example`, `https://target.example@evil.example`)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload in vulnerable redirect parameter`

<img width="1904" height="1143" alt="image" src="https://github.com/user-attachments/assets/b2a8e8f5-a2c8-43c5-8b57-7df9f4e7ee7b" />

`Screenshot 2: Screenshot shows the browser being redirected to the attacker-controlled domain`

<img width="1392" height="1132" alt="image" src="https://github.com/user-attachments/assets/fa5f0702-27db-4f5b-9d07-96cdecdf946a" />


## Impact

An attacker can perform the following actions using this vulnerability:

- It allows attackers to craft convincing phishing links that abuse the trust of the legitimate domain
- It can be used to steal user credentials or session tokens on a spoofed page after redirect
- It can be chained with OAuth/SSO flows to leak authorization codes or tokens to an attacker-controlled callback
- It can be used to distribute malware by redirecting users to a malicious download page
- It damages user trust in the legitimate application's links

## Recommendations for fix

Validate and restrict the [parameter name] parameter to ensure it cannot redirect to arbitrary external domains. This can be done by:

1. Maintain an allow-list of permitted redirect destinations (relative paths or specific trusted domains) and reject anything that doesn't match
2. Avoid accepting a full URL from user input at all — instead use an indirect reference, e.g. a lookup key/ID mapped server-side to a safe destination
3. If a relative path is expected, strictly validate that the value starts with a single `/` and does not contain `//`, `\`, or a scheme (`http:`, `https:`) to prevent protocol-relative or scheme-based bypasses
4. Show an interstitial "You are leaving [site]" warning page for any redirect to an external domain, requiring explicit user confirmation
5. Use a WAF (e.g. Cloudflare) as a defence-in-depth measure, though this should not be relied on as the primary fix
