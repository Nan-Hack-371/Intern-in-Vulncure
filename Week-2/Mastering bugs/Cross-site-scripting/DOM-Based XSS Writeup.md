# DOM-Based XSS via Insecure Dynamic Resource Loading

## Overview

This repository documents a real-world DOM-Based Cross-Site Scripting (XSS) vulnerability caused by insecure dynamic resource loading through a user-controlled parameter.

The vulnerability allowed an attacker to load arbitrary JavaScript files from an attacker-controlled server, resulting in client-side code execution and potential account takeover.

---

## Vulnerability Details

### Type

* DOM-Based XSS
* Client-Side Injection
* Session Hijacking
* Account Takeover

### Severity

High

### CWE

* CWE-79: Cross-Site Scripting
* CWE-829: Inclusion of Functionality from Untrusted Control Sphere

---

## Root Cause

The application dynamically loaded JavaScript resources using a user-controlled URL parameter:

```javascript
var eUrl = decodeURIComponent(urlObj.searchParams.get('eUrl'));

$.getScript({
    url: eUrl + '/deployment/env/' + env + '.config.js',
    cache: true
});
```

Because `eUrl` was not validated, attackers could host malicious JavaScript and force the application to execute it.

---

## Attack Chain

1. User clicks attacker-controlled URL.
2. Application reads `eUrl` parameter.
3. JavaScript is loaded from attacker infrastructure.
4. Malicious script executes in victim browser.
5. Session cookie is stolen.
6. Attacker hijacks user session.
7. Full account takeover becomes possible.

---

## Impact

* Arbitrary JavaScript Execution
* Session Cookie Theft
* Session Hijacking
* Phishing
* Account Takeover

---

## Remediation

* Validate and allowlist URLs.
* Restrict script sources using CSP.
* Avoid loading scripts from user input.
* Enable HttpOnly cookies.
* Enable SameSite cookie protection.

---

## Additional Research

This repository also includes research into a potential CSS Injection vector discovered during the assessment and explains why the issue was not exploitable in its current state.

---

## Key Lessons

* Always verify bug bounty scope before reporting.
* Review client-side JavaScript manually.
* Understand dangerous sinks such as:

  * eval()
  * innerHTML
  * document.write()
  * $.getScript()
* Look for user-controlled resource loading.

---

## Resources

* OWASP DOM XSS Prevention Cheat Sheet
* PortSwigger DOM-Based XSS Academy Labs
* OWASP Top 10
