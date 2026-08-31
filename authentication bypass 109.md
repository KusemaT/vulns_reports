   
## Title

Authentication Bypass via OTP request on https://kzlabs.in/subdomains/mobile/

## Vulnerability Type

Authentication Bypass

## Summary

Blinkit https://kzlabs.in/subdomains/mobile/ is vulnerable to an authentication bypass.
The application fails to properly enforce authentication checks on OTP request, allowing an attacker to access authenticated functionality or another user's account without providing valid credentials.

## Vulnerable Endpoint

`https://kzlabs.in/subdomains/mobile/` 

## Steps to Reproduce

1. Log out, or open the target endpoint in an unauthenticated/incognito session
2. Navigate directly to the following URL: `https://kzlabs.in/subdomains/mobile/`
   (Alternatively: intercept the login/authenticated request in Burp Suite and [ e.g. modify the `verify` parameter, remove the `Authorization` header, replay a request with a manipulated cookie/token, or change the `id` value to another user's ID])
3. Observe that the application grants access to protected content/functionality without requiring valid authentication

## Payload used

`Changed verify header request from false to true`

## Proof of Concept Request

`Screenshot 1: Screenshot shows the request sent without valid authentication`

<img width="1792" height="1120" alt="image" src="https://github.com/user-attachments/assets/ded7b931-78b0-4fff-bcff-9d385aeaf0d3" />

`Screenshot 2: Screenshot shows the protected page/data being accessed despite lack of valid authentication`

<img width="940" height="1099" alt="image" src="https://github.com/user-attachments/assets/530168b4-bdef-44ca-a54f-163f45183dc7" />


## Impact

An attacker can perform the following actions using this vulnerability:

- It allows attackers to access protected pages, functionality, or data without valid credentials
- It can lead to full account takeover of other users
- It allows unauthorized access to sensitive personal or business data
- It allows attackers to perform actions on behalf of other users
- It undermines the entire authentication/access control model of the application

## Recommendations for fix

Ensure authentication and authorization checks are consistently and correctly enforced. This can be done by:

1. Enforce server-side authentication checks on every protected endpoint — never rely on client-side checks, hidden fields, or obscurity (e.g. non-guessable URLs) alone
2. Validate session tokens/cookies/JWTs on every request and ensure they cannot be forged, replayed, or manipulated (check signature, expiry, and scope)
3. Implement proper access control checks tied to the authenticated user's identity, not to user-supplied parameters (e.g. don't trust an `id` or `role` value sent by the client)
4. Invalidate sessions/tokens on logout and enforce short expiry with secure refresh mechanisms
5. Apply the principle of least privilege and re-verify authorization at each step of multi-step processes, not just at login
6. Perform regular authenticated/unauthenticated access control testing across all endpoints, including APIs
