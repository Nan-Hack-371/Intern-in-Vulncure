# Day 9 - Internship at Vulncure

## Topics Covered

### 1. CVE Research & Analysis

Today I studied a publicly disclosed CVE involving an **Authenticated SSRF (Server-Side Request Forgery)** vulnerability in EspoCRM.

#### Key Learnings

* SSRF occurs when an application can be tricked into making requests on behalf of the attacker.
* Internal host validation can often be bypassed using alternative IP representations.
* Security controls should validate the final resolved IP address rather than relying solely on input validation.
* Attackers can abuse SSRF to access:

  * Internal services
  * Localhost applications
  * Cloud metadata endpoints
  * Internal APIs

#### Interesting Technique Learned

The writeup demonstrated bypassing localhost restrictions using an alternative IPv4 representation:

```text
127.0.0.1
```

bypassed as:

```text
0177.0.0.1
```

This highlighted the importance of understanding URL parsing and IP address normalization during SSRF testing.

---

## 2. Vulnerability Chaining

I also learned how attackers combine multiple low- and medium-severity vulnerabilities to achieve a critical impact.

### Example Chain

```text
User Enumeration
        +
Missing Rate Limiting
        +
Weak Reset Tokens
        ↓
Account Takeover
```

### Key Takeaways

* CVSS scores evaluate vulnerabilities individually.
* Attackers focus on attack paths, not individual bugs.
* Multiple low-severity findings can become critical when chained together.
* Always analyze how vulnerabilities interact with each other.

---

## 3. Mastering Bugs Research

### SSRF (Server-Side Request Forgery)

Today I continued researching SSRF as one of my target bug classes for mastery.

#### Areas Studied

* Basic SSRF concepts
* Internal network access via SSRF
* Localhost and loopback targeting
* SSRF filter bypass techniques
* Alternative IP address formats
* Cloud metadata exploitation
* SSRF impact assessment

#### Common SSRF Targets

```text
127.0.0.1
localhost
169.254.169.254
internal APIs
admin panels
```

#### SSRF Bypass Techniques Researched

* Decimal IP encoding
* Octal IP encoding
* Hexadecimal IP encoding
* Mixed encoding techniques
* DNS rebinding concepts
* Redirect-based bypasses

---

## Resources Studied

* Public CVE writeups
* Bug bounty reports
* OWASP SSRF documentation
* Research articles on vulnerability chaining
* Real-world exploitation case studies

---

## Progress Update

### Bugs Selected for Mastery

* SSRF
* XSS
* IDOR
* Cryptographic Issues
* Business Logic Vulnerabilities

### Today's Focus

✅ Studied an SSRF CVE writeup

✅ Learned SSRF localhost bypass techniques

✅ Researched vulnerability chaining

✅ Continued SSRF mastery research

### Next Steps

* Study more SSRF bypass techniques.
* Practice SSRF labs on PortSwigger.
* Analyze additional SSRF-related CVEs.
* Document SSRF payloads and testing methodology.
