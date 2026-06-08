# P4 | Content Spoofing via User-Controlled Template Parameters

## Overview

A Content Spoofing vulnerability was identified in the default installation templates of Shibboleth Service Provider (SP). The application allowed attackers to manipulate page resources such as stylesheets, logos, and support information through URL parameters.

As a result, an attacker could alter the appearance and content of trusted pages while the victim remained on the legitimate domain. This could be leveraged to conduct phishing attacks, social engineering campaigns, or credential harvesting.

The issue was assigned **CVE-2021-28963** and affected Shibboleth SP installations using vulnerable default templates.

---

## Severity

**P4 – Low Severity**

### Why P4?

* Does not directly expose sensitive data.
* Does not provide unauthorized system access.
* Requires user interaction.
* Impact primarily relies on social engineering.
* Can increase the effectiveness of phishing attacks.

---

## Vulnerability Type

* Content Spoofing
* UI Redressing
* Social Engineering Facilitation
* Phishing Assistance

---

## Affected Product

| Field         | Value                       |
| ------------- | --------------------------- |
| Product       | Shibboleth Service Provider |
| Vulnerability | Content Spoofing            |
| CVE           | CVE-2021-28963              |
| Severity      | P4 (Low)                    |
| Fixed Version | 3.2.1+                      |

---

## Description

Several Shibboleth error pages accepted values from URL parameters and inserted them directly into page templates.

Examples included:

* logoLocation
* stylesheet
* supportContact
* statusCode

These parameters were reflected into the generated page without sufficient restrictions.

An attacker could craft a URL that loads attacker-controlled resources such as CSS files or images, causing the trusted page to display arbitrary content.

Because the page is still served from a legitimate domain, users may trust the displayed content and follow malicious instructions.

---

## Impact

An attacker may be able to:

* Display misleading information on trusted domains.
* Replace legitimate branding with attacker-controlled branding.
* Hide original error messages.
* Trick users into visiting attacker-controlled websites.
* Increase the success rate of phishing campaigns.
* Conduct social engineering attacks.

### Potential Business Impact

* Loss of user trust.
* Increased phishing risk.
* Brand reputation damage.
* Credential theft through social engineering.

---

## Root Cause

The vulnerability existed because template variables were populated directly from URL parameters.

Example vulnerable variables:

```text
logoLocation
stylesheet
supportContact
statusCode
```

The application trusted user-supplied values and used them when rendering pages.

---

## Attack Scenario

### Step 1

Victim visits a specially crafted URL.

### Step 2

The application generates an error page.

### Step 3

Attacker-controlled stylesheet and resources are loaded.

### Step 4

Legitimate content is hidden or modified.

### Step 5

Victim sees attacker-supplied instructions while still viewing the trusted domain.

### Step 6

Victim follows malicious instructions, potentially leading to credential theft or phishing.

---

## Proof of Concept Summary

The researchers demonstrated that:

1. A Shibboleth error page could be triggered.
2. URL parameters could modify page resources.
3. A malicious stylesheet could completely alter the page appearance.
4. Users were presented with attacker-controlled content under the trusted domain.

No authentication was required.

---

## Real-World Impact

Researchers confirmed vulnerable deployments across multiple institutions including:

* Universities
* Research organizations
* Federation authentication services

The issue affected organizations relying on default Shibboleth templates before the patch.

---

## Mitigation

### Recommended Fix

Upgrade to:

```text
Shibboleth Service Provider 3.2.1 or later
```

### Additional Protections

* Do not allow user-controlled resource URLs.
* Restrict external stylesheet loading.
* Validate template variables.
* Apply Content Security Policy (CSP).
* Use allowlists for external resources.
* Review default templates before deployment.

---

## Detection Tips

Look for:

* URL parameters controlling page appearance.
* External CSS references loaded from user input.
* External image URLs loaded from request parameters.
* Error pages that dynamically render user-supplied values.

---

## CWE Mapping

### CWE-451

User Interface (UI) Misrepresentation of Critical Information

### CWE-79

Improper Neutralization of Input During Web Page Generation (Related Risk)

---

## References

* CVE-2021-28963
* Shibboleth Security Advisory
* OWASP Content Spoofing
* CWE-451 UI Misrepresentation

---

## Learning Notes

### Key Takeaways

* Not every vulnerability requires code execution to be dangerous.
* Trust in a legitimate domain can significantly increase phishing success rates.
* User-controlled styling and branding elements should never be trusted.
* Content Spoofing often appears low severity but can become impactful when combined with social engineering.

---

## Disclaimer

This write-up is intended for educational and defensive security purposes only. The information is provided to help understand Content Spoofing vulnerabilities and secure web applications against similar issues.
