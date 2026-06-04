# Vulnerability Chaining: How Low-Severity Bugs Become Critical Security Breaches

## Overview

Vulnerability chaining is the process of combining multiple low- or medium-severity vulnerabilities to achieve a high-impact outcome such as:

* Account Takeover (ATO)
* Privilege Escalation
* Remote Code Execution (RCE)
* Cloud Credential Theft
* Data Breach

Individually, each vulnerability may appear harmless. However, when combined strategically, they can create a critical attack path.

Original report:[here](https://medium.com/@KuboidSecureLayer/vulnerability-chaining-how-attackers-combine-low-severity-bugs-into-critical-breaches-e74f0cb35dc5)
---

## Why Vulnerability Chaining Matters

Security scanners typically:

* Test vulnerabilities individually.
* Assign CVSS scores to each finding.
* Do not evaluate how findings interact with one another.

Attackers, however:

* Build attack paths.
* Combine multiple weaknesses.
* Focus on achieving a final objective rather than exploiting a single bug.

### Key Difference

| Security Scanner                | Attacker               |
| ------------------------------- | ---------------------- |
| Sees individual vulnerabilities | Sees attack paths      |
| Rates findings separately       | Combines findings      |
| Focuses on severity score       | Focuses on impact      |
| Reports isolated risks          | Exploits chained risks |

---

# Attack Chain Example: Account Takeover

## Scenario

A security assessment identifies:

| Finding                  | Severity |
| ------------------------ | -------- |
| Verbose Error Messages   | Low      |
| Missing Rate Limiting    | Medium   |
| Weak Reset Token Entropy | Medium   |

No critical vulnerabilities are reported.

However, these findings can be chained into a complete account takeover.

---

## Step 1: User Enumeration

### Vulnerability

Verbose error messages disclose whether an account exists.

### Example Responses

#### Invalid User

```text
User not found
```

#### Valid User

```text
Reset email sent
```

### Impact

An attacker can identify valid accounts such as:

```text
ceo@company.com
admin@company.com
finance@company.com
```

### Result

* User enumeration becomes possible.
* High-value targets can be identified.

---

## Step 2: Unlimited Password Reset Requests

### Vulnerability

Password reset endpoint lacks rate limiting.

### Impact

An attacker can:

* Trigger unlimited reset emails.
* Automate requests.
* Flood the reset workflow.

### Example

```http
POST /password-reset
```

Thousands of requests can be sent without restriction.

### Result

Attacker gains unlimited opportunities to generate reset tokens.

---

## Step 3: Weak Reset Tokens

### Vulnerability

Password reset tokens are:

* Short
* Predictable
* Generated using weak randomness

### Example

```text
AB12CD
XZ93JK
```

### Impact

Token space becomes small enough to brute-force.

### Result

Valid reset tokens can be guessed within minutes.

---

## Complete Attack Path

### Chain

```text
Verbose Error Messages
        ↓
User Enumeration
        ↓
No Rate Limiting
        ↓
Unlimited Token Generation
        ↓
Weak Reset Tokens
        ↓
Token Brute Force
        ↓
Account Takeover
```

### Final Impact

An attacker:

1. Identifies CEO account.
2. Requests password resets repeatedly.
3. Brute-forces reset tokens.
4. Sets a new password.
5. Gains access to the account.

### Outcome

Critical Account Takeover (ATO)

---

# Why Automated Scanners Miss Vulnerability Chains

## Scanner Limitations

Scanners evaluate vulnerabilities independently.

### What Scanners See

```text
Finding 1 → Low
Finding 2 → Medium
Finding 3 → Medium
```

### What Attackers See

```text
Finding 1 + Finding 2 + Finding 3
        ↓
Critical Account Takeover
```

---

## CVSS Limitation

CVSS answers:

> How severe is this individual vulnerability?

It does NOT answer:

> What can an attacker achieve by combining multiple vulnerabilities?

---

# Additional Real-World Vulnerability Chains

---

## 1. Open Redirect + Reflected XSS

### Finding A

Open Redirect

```text
https://site.com?redirect=https://evil.com
```

### Finding B

Reflected XSS

```html
<script>alert(1)</script>
```

### Attack Chain

```text
Open Redirect
        +
Reflected XSS
        ↓
Trusted Phishing Link
        ↓
Session Theft
```

### Impact

* Phishing attacks
* Session hijacking
* Credential theft

---

## 2. SSRF + Cloud Metadata Endpoint

### Finding A

Server-Side Request Forgery (SSRF)

### Finding B

Accessible Cloud Metadata Service

Examples:

```text
AWS:
169.254.169.254

Azure:
169.254.169.254

GCP:
169.254.169.254
```

### Attack Chain

```text
SSRF
        ↓
Metadata Access
        ↓
Cloud Credentials
        ↓
Cloud Environment Compromise
```

### Impact

* IAM credential theft
* Cloud account compromise
* Data exposure

---

## 3. IDOR + Information Disclosure

### Finding A

IDOR exposes:

* Email addresses
* User information
* Phone numbers

### Finding B

Information disclosure identifies:

* Administrators
* Privileged users

### Attack Chain

```text
IDOR
        +
Information Disclosure
        ↓
Admin Identification
        ↓
Targeted Phishing
        ↓
Privilege Compromise
```

### Impact

* Administrative account compromise
* Targeted attacks
* Privilege escalation

---

# Severity Miscalculation Problem

A common mistake is focusing only on CVSS scores.

### Example

| Finding            | CVSS |
| ------------------ | ---- |
| User Enumeration   | 3.1  |
| No Rate Limiting   | 5.0  |
| Weak Token Entropy | 5.3  |

### Scanner Conclusion

```text
No Critical Findings
```

### Real-World Conclusion

```text
Critical Account Takeover
```

The overall risk is determined by the attack path, not by averaging severity scores.

---

# What Security Researchers Should Look For

When reviewing findings:

## Ask These Questions

### Shared Functionality

* Do multiple findings affect the same feature?
* Do they affect the same authentication flow?

### Information Leaks

* Does a low-severity disclosure enable another attack?

### Authentication Weaknesses

* Can enumeration be combined with brute force?
* Can weak tokens be abused?

### Access Control

* Can an IDOR expose data useful elsewhere?

### Chaining Potential

* What can an attacker do after exploiting this bug?
* What does this unlock?

---

# Key Takeaways

* Attackers exploit attack paths, not individual findings.
* Low-severity vulnerabilities can become critical when chained.
* CVSS scores evaluate vulnerabilities in isolation.
* Automated scanners rarely identify complex attack chains.
* Manual testing remains essential for discovering real-world risk.
* Security reports should document attack paths, not just severity ratings.

---

# Conclusion

A vulnerability should never be assessed solely by its individual severity score.

The real question is:

> What can an attacker achieve by combining the weaknesses present in this application?

Understanding vulnerability chaining helps security researchers, bug bounty hunters, and defenders identify the true risk hidden behind seemingly harmless findings.
