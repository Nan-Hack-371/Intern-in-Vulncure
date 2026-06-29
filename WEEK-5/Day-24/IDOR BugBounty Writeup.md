# Facebook Groups Notes Media IDOR

> Analysis of a publicly disclosed Facebook Bug Bounty report where an Insecure Direct Object Reference (IDOR) vulnerability allowed unauthorized access to private Facebook Groups Notes cover media.

> **Research Source**
>
> Raja Sudhakar
>
> Public Disclosure: August 31, 2023

---

## Overview

This repository contains an educational analysis of a publicly disclosed Facebook vulnerability.

The issue existed in the Facebook Groups Notes feature where improper authorization checks allowed an attacker to reference media belonging to another private group.

Facebook acknowledged the report, fixed the vulnerability, and awarded the researcher **$10,000**.

---

## Vulnerability Information

| Field | Value |
|--------|-------|
| Platform | Facebook |
| Component | Groups Notes |
| Vulnerability | IDOR |
| Severity | High |
| CWE | CWE-639 |
| OWASP | Broken Access Control |
| Reward | $10,000 |
| Status | Fixed |

---

# What is IDOR?

An **Insecure Direct Object Reference (IDOR)** occurs when an application exposes an internal object identifier without properly verifying whether the requesting user is authorized to access that object.

Instead of validating ownership or permissions, the server trusts the client-supplied identifier.

Example:

```
User A owns Resource A
User B changes the Resource ID
Server returns Resource A
```

The vulnerability is caused by missing authorization, **not** by predictable identifiers alone.

---

# Affected Feature

Facebook Notes supported:

- Long-form posts
- Rich text
- Images
- Cover media
- Hyperlinks
- Formatting

Each note could reference uploaded media using an internal media identifier.

---

# Root Cause

The server accepted a client-supplied media identifier without verifying that:

- the media belonged to the current user
- the media belonged to the same Facebook Group
- the requester had permission to access the referenced object

Because of this missing authorization validation, an attacker could reference media outside their authorization boundary.

---

# High-Level Attack Flow

```text
Attacker creates a new Note
            │
            ▼
Application accepts Media ID
            │
            ▼
Attacker replaces the Media ID
            │
            ▼
Server fails authorization check
            │
            ▼
Private media becomes accessible
```

---

# Impact

Possible impacts included:

- Unauthorized viewing of private group media
- Privacy violation
- Information disclosure
- Cross-group data exposure
- Broken access control

---

# Security Lessons

Developers should never trust object identifiers supplied by clients.

Every object reference should be validated against:

- ownership
- permissions
- group membership
- resource visibility
- authorization policy

---

# Secure Design Recommendations

✔ Validate ownership before returning objects

✔ Perform authorization checks on every request

✔ Avoid trusting client-controlled identifiers

✔ Enforce server-side access control

✔ Apply least-privilege principles

✔ Log unauthorized access attempts

---

# Related OWASP Categories

- Broken Access Control
- Insecure Direct Object References
- Authorization Failures

---

# MITRE Mapping

| Category | ID |
|----------|----|
| CWE | CWE-639 |
| OWASP Top 10 | A01:2021 Broken Access Control |

---

# Learning Objectives

After studying this case, readers should understand:

- How IDOR vulnerabilities occur
- Why authorization checks are critical
- Difference between authentication and authorization
- Common IDOR patterns
- Secure object reference handling

---

# Repository Contents

```
writeup/
│
├── vulnerability-analysis.md
├── root-cause.md
├── impact.md
├── remediation.md
└── references.md
```

---

# References

- OWASP Broken Access Control
- OWASP IDOR Prevention Cheat Sheet
- CWE-639
- Facebook Bug Bounty Program
- Public disclosure by Raja Sudhakar

---

# Disclaimer

This repository is intended solely for educational and defensive security purposes.

It summarizes a publicly disclosed and remediated vulnerability to help developers and security practitioners understand authorization flaws and secure coding practices. It does not contain exploit instructions or targets for unauthorized testing.

---

## Author

**Nandita Shrivastav**

Cybersecurity Student

Bug Bounty Learner

TryHackMe Learner
