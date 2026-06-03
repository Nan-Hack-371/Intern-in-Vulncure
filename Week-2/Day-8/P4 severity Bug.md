# P4 Bug Study - Stored XSS (Privileged User → No Privilege Elevation)

* Category: Stored Cross-Site Scripting (XSS)

* Severity: P4

* Source: Bugcrowd VRT

---

## Bug Summary

This vulnerability occurs when a privileged user (Admin/Owner) can store malicious JavaScript inside an application.

The payload executes when another user views the affected page.

However, the attacker does not gain higher privileges because they already have administrative access.

Because there is no privilege escalation, Bugcrowd classifies this issue as P4.

---

## Why Is This P4?

* Attacker already has Admin access.

* No additional permissions are gained.

* No vertical privilege escalation occurs.

* Impact is limited compared to normal Stored XSS.

---

## Root Cause

* Missing input sanitization.

* Missing output encoding.

* User-controlled content rendered directly in HTML.

---

## Important Concept: Output Encoding

Output encoding converts dangerous HTML into harmless text.

Example:

```html
<script>alert(1)</script>
```

becomes:

```html
&lt;script&gt;alert(1)&lt;/script&gt;
```

As a result, the browser displays the text instead of executing JavaScript.


---

## Example PoC

Payload:

```html
<img src=x onerror=alert(document.domain)>
```

Steps:

1. Login as Admin.

2. Navigate to Announcement or Settings page.

3. Insert payload.

4. Save changes.

5. Login as Normal User.

6. Visit affected page.

7. JavaScript executes.

---

## Possible Impact

* Session Theft

* Internal Phishing

* Forced User Actions

* UI Manipulation

---

## Where To Look For This Bug?

* Announcements

* Company Description

* Internal Notes

* User Management Comments

* Custom Branding Features

* Dashboard Messages

---

## Key Learnings

* Stored XSS can exist even in Admin-only features.

* High privileges do not make XSS safe.

* Output encoding is one of the most important defenses against XSS.

* Impact determines severity, not just the vulnerability type.

* A Stored XSS performed by an Admin is usually rated lower because no privilege escalation occurs.

---

## Personal Notes

* Learned how Bugcrowd classifies XSS based on attacker privileges.

* Understood the difference between high-impact Stored XSS and low-impact Stored XSS.

* Learned why some Stored XSS vulnerabilities receive a P4 rating instead of P2/P3.
