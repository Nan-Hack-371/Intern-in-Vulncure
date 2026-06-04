# Server-Side Request Forgery (SSRF)

* Severity: High

* OWASP Category: Server-Side Request Forgery

* Common Impact: Internal Network Access, Data Exposure, Cloud Credential Theft

---

## What is SSRF?

* Server-Side Request Forgery (SSRF) is a vulnerability that allows an attacker to force a server to make requests on their behalf.

* The attacker controls the destination of the request.

* The request originates from the application server, not from the attacker.

* Internal systems often trust requests coming from application servers.

---

## How SSRF Happens

* An application accepts a URL, hostname, or path from a user.

* The server uses that input to make a backend request.

* Input validation is missing or insufficient.

* The attacker modifies the destination of the request.

* The server unknowingly sends requests to attacker-controlled or internal systems.

---

## Types of SSRF

### Regular SSRF

* The response is returned to the attacker.

* Internal resources can be viewed directly.

* Easier to exploit and verify.

---

### Blind SSRF

* The response is not visible.

* The server still performs the request.

* Verification requires indirect techniques.

* Commonly confirmed using external interaction services.

---

## Common SSRF Targets

* Internal Admin Panels

* Internal APIs

* Monitoring Systems

* Backend Databases

* Cloud Metadata Services

* Internal Network Services

---

## Cloud Metadata Endpoint

* One of the most common SSRF targets.

```text id="w6p3m7"
http://169.254.169.254
```

* May expose:

  * Temporary Credentials

  * IAM Roles

  * Instance Information

  * Access Tokens

---

## Common SSRF Injection Points

* URL Parameters

* Image Fetchers

* PDF Generators

* Webhooks

* Import Features

* Hidden Form Fields

* API Endpoints

---

## Impact

* Access to Internal Endpoints

* Internal Network Reconnaissance

* Sensitive Data Disclosure

* Cloud Credential Theft

* Authentication Token Leakage

* Resource Restriction Bypass

---

## Attack Scenarios

### Internal Admin Access

* Access internal dashboards not exposed to the internet.

---

### Internal Network Scanning

* Discover hosts and services.

* Identify open ports.

* Map internal infrastructure.

---

### Cloud Metadata Theft

* Access cloud instance metadata.

* Obtain temporary credentials.

* Escalate privileges.

---

### Sensitive Data Exposure

* Access internal APIs.

* Retrieve confidential records.

* Expose application secrets.

---

## Prevention

* Use strict allowlists for URLs and hosts.

* Block internal IP ranges.

* Disable unnecessary URL fetching.

* Validate all user-supplied URLs.

* Restrict outbound network access.

* Monitor abnormal server-side requests.

---

## References

* OWASP SSRF Prevention Cheat Sheet

* PortSwigger Web Security Academy

* TryHackMe SSRF Labs

* Real-World Bug Bounty Reports
