# Day 21 – Cyberseacurity Internship

## Date

June 2026

---

# Objective

Today's focus was on improving practical bug hunting skills by studying real-world vulnerabilities, analyzing public write-ups, and understanding how security researchers discover and report vulnerabilities.

---

# Activities Completed

## 1. Studied SSRF Filter Bypass Write-up

### Topic

Multiple HTTP Redirects to Bypass SSRF Protections

### What I Learned

* How applications implement SSRF protections.
* Common SSRF filtering approaches.
* Importance of validating redirect chains.
* How researchers identify weaknesses in URL validation logic.
* Difference between Blind SSRF and Full SSRF.

### Key Takeaway

Security controls must validate every redirect destination instead of trusting only the initial URL.

---

## 2. Analyzed Browser Side-Channel Research

### Topic

Cross-Site Frame Leakage (CSFL)

### What I Learned

* Browser behavior can leak sensitive information.
* Same-Origin Policy does not always prevent information disclosure.
* Metadata can be valuable even when direct data access is impossible.
* Privacy-related vulnerabilities can still have security impact.

### Key Takeaway

Application state differences can sometimes be detected through browser side channels.

---

## 3. CVE Research

### Areas Reviewed

* XSS Vulnerabilities
* DOM Clobbering
* SSRF
* Information Disclosure
* Client-Side Security Issues

### Focus Points

* Root Cause Analysis
* Attack Surface
* Security Impact
* Mitigation Techniques
* Vendor Patches

### Key Takeaway

Understanding why a vulnerability exists is more valuable than simply memorizing the exploit.

---


# Skills Improved

## Technical Skills

* Vulnerability Analysis
* CVE Research
* Attack Surface Identification
* Security Documentation
* Risk Assessment

## Soft Skills

* Research Methodology
* Technical Reading
* Critical Thinking
* Structured Documentation

---

# Challenges Faced

### Understanding Large Applications

Many bug bounty targets contain numerous features and workflows, making it difficult to determine where to start testing.

### Solution

Focus on:

* Core functionality
* User-controlled inputs
* Authentication features
* URL processing functionality
* File handling features

---

# Resources Used

* Public Bug Bounty Write-ups
* CVE Advisories
* Security Research Blogs
* GitHub Security Advisories
* OWASP Resources

---

# Key Learnings of the Day

* Reconnaissance is critical before testing.
* Studying real-world vulnerabilities improves practical understanding.
* Root cause analysis is essential for learning.
* Documentation is an important part of security research.
* Understanding application functionality helps identify potential attack surfaces.

---

# Day Summary

Today was focused on vulnerability research and practical bug hunting methodology. I studied real-world SSRF and browser-based information disclosure vulnerabilities, reviewed public CVEs, and practiced converting vulnerability disclosures into professional GitHub-style documentation. This helped strengthen my understanding of vulnerability analysis, root cause identification, and security reporting.
