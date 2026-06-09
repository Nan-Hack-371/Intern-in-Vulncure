# Day-12: Vulnerability Research, CVE Analysis & Reconnaissance

## Overview

On Day-12, I focused on studying real-world vulnerability reports, analyzing a recent CVE, and strengthening my reconnaissance knowledge through bug bounty learning resources. The goal was to understand how vulnerabilities are discovered, exploited, and documented in real-world scenarios.

---

## Tasks Completed

### 1. Bug Bounty Writeup Analysis

#### Writeup 1: DOM-Based XSS Leading to Account Takeover

**Vulnerability Type:** DOM-Based Cross-Site Scripting (XSS)

**Key Learnings:**

* Identified how user-controlled parameters can be abused to load malicious JavaScript files.
* Learned how insecure dynamic resource loading can lead to client-side code execution.
* Understood the attack chain from DOM XSS to session hijacking and account takeover.
* Studied root cause analysis and mitigation strategies such as input validation and Content Security Policy (CSP).

---

#### Writeup 2: Session Management Vulnerability

**Title:** Removing Linked Identity Does Not Invalidate Associated Sessions

**Vulnerability Type:** Broken Authentication / Session Management

**Key Learnings:**

* Studied OAuth account linking and authentication workflows.
* Learned how active sessions remained valid even after linked identities were removed.
* Understood the importance of proper session invalidation after sensitive account changes.
* Explored the impact of unauthorized access through stale sessions.

---

### 2. CVE Analysis

#### CVE-2024-29182 – Collabora Online Stored XSS

**Vulnerability Type:** Stored Cross-Site Scripting (Stored XSS)

**Key Learnings:**

* Analyzed how unsanitized user-controlled content was rendered inside tooltips.
* Traced the complete execution flow from WebSocket communication to frontend rendering.
* Learned how unsafe usage of `innerHTML` can lead to stored XSS vulnerabilities.
* Studied the impact, attack vectors, and secure coding practices to prevent similar issues.

---

### 3. Bug Bounty Book Study

#### Chapter 6 – Reconnaissance

Completed Chapter 6 of the Bug Bounty book focusing on reconnaissance methodologies.

**Topics Covered:**

* Passive Reconnaissance
* Active Reconnaissance
* Asset Discovery Techniques
* Subdomain Enumeration
* Technology Fingerprinting
* Content Discovery
* Attack Surface Mapping


**Key Takeaways:**

* Reconnaissance is the foundation of successful bug hunting.
* Comprehensive asset discovery increases the chances of finding vulnerabilities.
* Combining multiple recon tools provides better coverage of the attack surface.
* Organized recon workflows improve efficiency during security assessments.


---

## Resources

* Real-World Bug Bounty Writeups
* CVE-2024-29182 Advisory
* Bug Bountyv1 by ghostlulz – Chapter 6


---

## Outcome

Day-12 strengthened my understanding of client-side vulnerabilities, session management flaws, stored XSS attacks, and reconnaissance techniques. The practical analysis of real-world writeups and CVEs helped improve my ability to identify root causes, understand attack flows, and document security findings effectively.
