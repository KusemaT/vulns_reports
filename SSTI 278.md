## Title

Server-Side Template Injection via [Subject] Parameter on [Executive Security audit report designer] Page

## Vulnerability Type

Server-Side Template Injection (SSTI)

## Summary

[CloudGuard] [https://kzlabs.in/subdomains/jinja2/] [subject] parameter is vulnerable to server-side template injection.
The application embeds user-controlled input directly into a server-side template (e.g. [Jinja2 / Twig / Freemarker / Velocity / Handlebars — identify once confirmed]) before rendering, without proper sanitisation. This allows an attacker to inject template syntax that is evaluated by the templating engine, which can be used to disclose sensitive server-side data and, depending on the engine and sandboxing, escalate to remote code execution.

## Vulnerable Endpoint

`https://kzlabs.in/subdomains/jinja2/`

## Steps to Reproduce

1. Navigate to the following URL, or submit the following value in the [subject] field: `https://kzlabs.in/subdomains/jinja2/`
2. Observe that the payload is evaluated by the server (e.g. the mathematical expression is calculated and rendered as `49` instead of being reflected literally as `{{7*7}}`), confirming the injection point and template engine
3. Escalate the payload to disclose server-side objects/config, or achieve command execution, depending on the identified engine

## Payload used

`{{7*7}}`

(Other useful payloads to identify the engine and demonstrate impact: `${7*7}` for Freemarker/JSP EL, `#{7*7}` for OGNL, `<%= 7*7 %>` for ERB, and for Jinja2 escalation to RCE: `{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}`)

## Proof of Concept Request

`Screenshot 1: Screenshot shows payload submitted in vulnerable [field/parameter name]`

<img width="1434" height="1143" alt="image" src="https://github.com/user-attachments/assets/03723314-09a3-4539-998f-149666f84112" />


`Screenshot 2: Screenshot shows the payload being evaluated by the template engine (e.g. 49 returned instead of literal payload)`

<img width="1434" height="1143" alt="image" src="https://github.com/user-attachments/assets/824721b4-7284-4ef2-817d-b0fb3d7b5161" />


## Impact

An attacker can perform the following actions using this vulnerability:

- It allows attackers to confirm and fingerprint the server-side templating engine in use
- It allows attackers to disclose sensitive server-side data, including application source code, configuration, and environment variables
- Depending on the templating engine and available sandboxing, it can be escalated to full remote code execution on the server
- It can result in complete compromise of the server, including access to other applications and the internal network
- It undermines the confidentiality, integrity, and availability of the application and its data

## Recommendations for fix

Ensure user-supplied input is never rendered as part of a template. This can be done by:

1. Never embed user input directly into a template string; instead pass it as data/context to the template rather than as part of the template logic itself
2. Use a "logic-less" templating engine (e.g. Mustache) where feasible, which limits the syntax available for injection
3. If the templating engine supports it, use a sandboxed execution environment that restricts access to dangerous built-ins/objects
4. Apply strict input validation/allow-listing on any input that must be used in a templating context
5. Keep the templating engine and framework patched, and follow vendor guidance on safely handling untrusted input
6. Use a WAF (e.g. Cloudflare) as a defence-in-depth measure, though this should not be relied on as the primary fix
