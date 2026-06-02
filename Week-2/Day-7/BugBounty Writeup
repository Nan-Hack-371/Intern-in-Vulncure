# Reflected Cross-Site Scripting (XSS)

---

* Platform: HackerOne

* Vulnerability Type: Reflected XSS

* Reported: 26/05/2018

* Bounty Awarded: $100

---

## Description

---

* While performing reconnaissance on the target application, multiple endpoints and pages were reviewed.

* During testing, an unusual URL path was identified:

```text
https://example.com/abc/]
```

* The page loaded normally, but further investigation revealed interesting behavior in the page source.

---

## Vulnerability

---

* User-controlled URL path data was reflected inside an HTML comment.

* The application failed to properly sanitize special characters supplied in the URL path.

* An attacker could terminate the comment and inject arbitrary HTML or JavaScript.

---

## Affected Component

---

* URL Path Handling

* HTML Comment Context

* Multiple pages across the affected domain

---

## Root Cause

---

* User input from the URL path was embedded directly into an HTML comment.

* Special characters were not filtered or encoded.

* The application allowed comment termination using attacker-controlled input.

---

## Technical Details

---

* Inspection of the page source revealed a commented string containing the URL path.

* Any value supplied in the path appeared inside the comment.

Example:

```html
<!-- /abc/] -->
```

* This indicated that user input was reflected without proper sanitization.

---

## Exploitation

---

1. Visit a page containing the vulnerable URL path reflection.

2. View the page source.

3. Confirm that the URL path is reflected inside an HTML comment.

4. Close the comment using a comment termination sequence.

5. Inject arbitrary HTML or JavaScript.

6. Reload the page and observe script execution.

---

## Payload

---

```html
]--><script>alert(1)</script>
```

---

## Proof of Concept

---

Vulnerable URL:

```text
https://example.com/abc/]--><script>alert(1)</script>
```

Result:

```javascript
alert(1)
```

* The JavaScript payload executed successfully.

* Reflected Cross-Site Scripting was confirmed.

---

## Impact

---

* Arbitrary JavaScript execution.

* Session theft.

* Credential phishing.

* DOM manipulation.

* User impersonation.

* Client-side attacks against visitors.

---

## Additional Findings

---

* The same behavior was observed across multiple pages.

* Every page on the affected domain reflected the URL path inside an HTML comment.

* As a result, the vulnerability affected the entire domain rather than a single endpoint.

---

## Mitigation

---

* Never place user-controlled input directly inside HTML comments.

* Apply proper output encoding.

* Sanitize special characters before rendering.

* Implement a Content Security Policy (CSP).

* Validate all URL path inputs server-side.

---

## Conclusion

---

* A reflected Cross-Site Scripting vulnerability was discovered due to unsafe reflection of URL path data inside HTML comments.

* By terminating the comment and injecting a script tag, arbitrary JavaScript execution was achieved.

* The issue affected multiple pages across the domain and was successfully reported through HackerOne.
