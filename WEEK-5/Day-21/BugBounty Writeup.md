# Multiple HTTP Redirects to Bypass SSRF Protections

## Overview

### Vulnerability Type

Server-Side Request Forgery (SSRF)

### Severity

Medium - High (Depending on Internal Network Exposure)

### Category

SSRF Filter Bypass

### Discovery Type

Bug Bounty Research

### Status

Reported and Triaged

---

# Executive Summary

During security testing of a marketing platform, several features were identified that accepted user-supplied URLs and performed server-side requests.

The application implemented SSRF protections intended to prevent requests to internal resources such as localhost and private network addresses. Initial testing suggested that these protections were effective.

However, further analysis revealed a validation weakness in the application's redirect handling logic. The application validated only the initial redirect destination while failing to consistently validate subsequent redirects.

This behavior enabled a redirect chain that ultimately caused the server to access restricted internal resources, resulting in a Server-Side Request Forgery vulnerability.

---

# Vulnerability Description

## What is SSRF?

Server-Side Request Forgery (SSRF) occurs when an application fetches a user-controlled URL without properly validating the destination.

Instead of accessing only intended external resources, the server may be tricked into making requests to:

* Internal systems
* Cloud metadata services
* Administrative interfaces
* Private APIs
* Loopback addresses

---

## Affected Functionality

The target application contained several URL-processing features.

Example API pattern:

```http
GET /api/campaign/v3/check-snippet?url=https://example.com
```

The server would:

1. Receive a user-supplied URL.
2. Make a server-side request.
3. Return a success response if the request completed successfully.

This behavior created an SSRF attack surface.

---

# Root Cause Analysis

## Intended Protection

The application attempted to block requests targeting:

* localhost
* Loopback addresses
* Internal network destinations
* Restricted IP ranges

The validation mechanism inspected supplied URLs and attempted to identify dangerous destinations.

---

## Validation Weakness

Testing revealed that redirects were followed by the server.

The security control validated the first redirect destination but failed to consistently enforce the same validation on later redirects in the chain.

As a result:

```text
Initial URL
        ↓
Redirect #1 (validated)
        ↓
Redirect #2 (not validated correctly)
        ↓
Restricted Internal Resource
```

This validation gap created an SSRF bypass opportunity.

---

# Vulnerability Discovery Process

## Initial Reconnaissance

During testing, several API endpoints were identified that accepted user-controlled URLs.

Key observations:

* Requests originated from cloud infrastructure.
* Outbound connections were permitted.
* External URLs were reachable.
* Response behavior indicated server-side fetching.

---

## SSRF Validation Testing

A series of SSRF payload categories were evaluated, including:

* Loopback addresses
* Alternative IP encodings
* Hostname variations
* DNS-based techniques
* Redirect-based techniques

The application successfully blocked many common SSRF payloads.

Examples included:

* localhost references
* Standard loopback addresses
* Various alternative address representations

---

## Redirect Behavior Analysis

Attention shifted to redirect processing.

Testing confirmed:

* HTTP redirects were followed.
* The first redirect location was inspected.
* Validation became inconsistent deeper in the redirect chain.

This behavior suggested the possibility of bypassing destination restrictions through multiple redirect hops.

---

# How the Vulnerability Was Exploited

## Attack Flow

### Step 1 – User-Controlled URL Submission

The attacker supplies a URL to a vulnerable endpoint.

---

### Step 2 – Server Requests External Resource

The application initiates an outbound request to the supplied URL.

---

### Step 3 – Redirect Chain Processing

The external resource responds with redirect instructions.

The application automatically follows the redirects.

---

### Step 4 – Validation Gap Triggered

The first redirect destination is inspected by the security control.

Subsequent redirect destinations are not subjected to the same level of validation.

---

### Step 5 – Internal Resource Access

The redirect chain eventually reaches a restricted destination.

The server performs the request on behalf of the attacker.

---

# Impact Assessment

## Blind SSRF Impact

In several instances, the application did not return the response body.

This resulted in Blind SSRF.

Potential impact includes:

* Internal service discovery
* Network mapping
* Cloud environment enumeration
* Access to internal APIs

---

## Full SSRF Impact

One affected endpoint provided sufficient response information to facilitate deeper internal reconnaissance.

Potential impact includes:

* Internal asset discovery
* Internal endpoint enumeration
* Exposure of administrative services
* Increased attack surface visibility

---

# Security Impact

## Confidentiality

### Medium

Internal infrastructure details may become accessible.

Examples:

* Hostnames
* Internal URLs
* Service metadata

---

## Integrity

### Low

No direct modification of data was observed.

---

## Availability

### Low

The vulnerability primarily affected confidentiality rather than service availability.

---

# Why Existing Protections Failed

The application's SSRF defense strategy relied heavily on destination validation.

The protection assumed:

* Initial validation would remain valid throughout request processing.
* Redirect chains could be trusted after the first inspection.

This assumption was incorrect.

Security controls must validate every redirect destination individually.

---

# Remediation

## Validate Every Redirect Hop

Each redirect destination should be revalidated before the next request is issued.

Validation should not occur only once.

---

## Disable Automatic Redirect Following

Applications should avoid automatically following redirects when processing user-supplied URLs.

---

## Implement Network Allowlisting

Allow outbound requests only to approved destinations.

Examples:

* Trusted APIs
* Business partners
* Required external services

---

## Block Internal Address Ranges

Deny requests targeting:

* Loopback addresses
* Private IP ranges
* Link-local addresses
* Cloud metadata services

---

## Use SSRF-Safe Fetch Services

Route all URL fetch operations through a dedicated service that enforces:

* Destination validation
* DNS validation
* Redirect validation
* Network restrictions

---

# Lessons Learned

## Redirects Are Part of the Attack Surface

Many SSRF protections focus only on the user-supplied URL while ignoring the complete redirect chain.

---

## Single-Layer Validation Is Insufficient

Security checks must remain effective throughout the entire request lifecycle.

---

## Blind SSRF Can Still Be Dangerous

Even without direct response access, attackers may gain valuable insight into internal infrastructure.

---

# References

## Original Research

Koru AI – Multiple HTTP Redirects to Bypass SSRF Protections [here](https://infosecwriteups.com/multiple-http-redirects-to-bypass-ssrf-protections-45c894e5d41c)

Published: January 29, 2022

---

## SSRF Resources

### PayloadsAllTheThings

https://github.com/swisskyrepo/PayloadsAllTheThings

### SSRF Testing Methodology

https://github.com/cujanovic/SSRF-Testing

### OWASP SSRF Prevention Cheat Sheet

https://cheatsheetseries.owasp.org

---

# Key Takeaways

* User-controlled URL fetching introduced SSRF risk.
* Basic filtering successfully blocked common SSRF payloads.
* Redirect handling contained a validation weakness.
* Inconsistent validation across redirect chains enabled SSRF bypass.
* Multiple affected endpoints were discovered.
* Proper redirect validation would have prevented the issue.

---

**Research Category:** Web Application Security

**Vulnerability Class:** Server-Side Request Forgery (SSRF)

**Subcategory:** Redirect-Based SSRF Bypass

**Severity:** Medium–High

**Status:** Reported and Resolved
