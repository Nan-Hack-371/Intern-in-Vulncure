# Always Test 404 Not Found Assets in Bug Bounties

## Overview

Many bug bounty hunters ignore subdomains that return a simple **404 Not Found** response because they appear to have no useful content.

However, a website returning 404 on its root directory (`/`) does not necessarily mean the entire application is empty. Hidden files, static pages, development artifacts, and forgotten content may still exist and can expose serious security vulnerabilities.

This write-up demonstrates why every discovered asset deserves investigation, even when the homepage appears inaccessible You can view original writup [here](https://blog.viktormares.com/always-test-404-not-found-in-bug-bounties-2be47801b4c0)

---

# Objective

The goal of this methodology is to:

* Identify overlooked attack surfaces.
* Discover hidden content.
* Find exposed credentials.
* Locate vulnerable functionality.
* Increase bug bounty coverage.
* Reduce missed opportunities.

---

# Description

A common mistake during reconnaissance is dismissing subdomains that display:

```text
404 Not Found
```

Many hunters immediately move on to other assets.

However, content may still exist behind:

* Hidden directories
* Static pages
* Legacy applications
* Development files
* Test environments
* Backup resources

Therefore, every discovered asset should undergo content discovery before being discarded.

---

# How It Happened

## Initial Discovery

During wildcard subdomain enumeration:

```text
*.example.com
```

A subdomain was discovered:

```text
example1.example.com
```

The root page returned:

```text
404 Not Found
```

At first glance, the website appeared empty.

---

## Further Investigation

Instead of ignoring the asset, content discovery was performed.

### Content Discovery Process

* Directory enumeration
* File discovery
* Static page identification
* Source code review

The scan revealed several hidden resources that were not accessible from the root page.

---

# Example 1 - Exposed AWS Credentials

## Discovery

Content discovery uncovered:

```text
/test.html
```

The page itself appeared harmless.

However, reviewing the page source code revealed exposed AWS credentials.

---

## Vulnerability

Sensitive cloud credentials were embedded directly in client-side source code.

Exposed information included:

* AWS Access Key
* AWS Secret Key

---

## How It Was Exploited

### Attacker Actions

1. Discover hidden page.
2. Review HTML source code.
3. Extract AWS credentials.
4. Verify credential validity.
5. Enumerate cloud permissions.

---

## Impact

Successful exploitation may lead to:

* Unauthorized cloud access
* S3 bucket exposure
* Data disclosure
* Infrastructure compromise
* Privilege escalation
* Business impact

---

## Severity

Potentially High or Critical depending on assigned permissions.

---

# Example 2 - Reflected Cross-Site Scripting (XSS)

## Discovery

Another subdomain returned:

```text
404 Not Found
```

Content discovery identified:

```text
/sites
```

The endpoint accepted a parameter:

```text
?SiteID=
```

---

## Vulnerability

The SiteID parameter reflected user input into the page without proper sanitization.

This resulted in:

* Reflected Cross-Site Scripting (XSS)

---

## How It Happened

The application accepted user-controlled input and rendered it back to the browser.

Input validation and output encoding were missing.

---

## How It Was Exploited

An attacker could inject JavaScript through the vulnerable parameter.

Example proof-of-concept:

```text
?SiteID=<payload>
```

The payload was reflected and executed within the victim's browser.

---

## Impact

Successful exploitation could result in:

* Session theft
* Account compromise
* Phishing attacks
* Credential theft
* User impersonation
* Malicious redirections

---

## Severity

Medium (P3)

Severity may increase depending on:

* Authentication context
* Sensitive functionality
* Administrative access

---

# Key Lessons Learned

## Never Ignore 404 Responses

A 404 homepage does not mean:

* No content exists
* No vulnerabilities exist
* The asset is out of scope
* The application is inactive

---

## Always Perform Content Discovery

Before abandoning an asset:

* Enumerate directories
* Enumerate files
* Review source code
* Inspect JavaScript files
* Search for hidden endpoints
* Look for development artifacts

---

## Hidden Assets Often Contain

* API endpoints
* Test environments
* Backup files
* Configuration files
* Secrets
* Credentials
* Vulnerable functionality

---

# Methodology Workflow

```text
Subdomain Discovery
        │
        ▼
404 Not Found Asset
        │
        ▼
Content Discovery
        │
        ▼
Hidden Pages Found
        │
        ▼
Source Code Review
        │
        ▼
Vulnerability Discovery
        │
        ▼
Impact Assessment
        │
        ▼
Bug Bounty Report
```

---

# Findings Summary

| Example   | Finding                 | Impact                              |
| --------- | ----------------------- | ----------------------------------- |
| Example 1 | Exposed AWS Credentials | Potential Infrastructure Compromise |
| Example 2 | Reflected XSS           | Client-Side Account Compromise      |

---

# Conclusion

One of the most valuable lessons in bug bounty hunting is that a seemingly useless asset can hide significant vulnerabilities.

A subdomain returning only a 404 response should never be immediately discarded. Proper content discovery and source code analysis can uncover hidden functionality, exposed secrets, and exploitable vulnerabilities that many researchers overlook.

Sometimes the most rewarding findings are hidden behind the pages everyone else ignores.
