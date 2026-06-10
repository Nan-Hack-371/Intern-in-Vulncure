# Day-13: CVE Analysis, Bug Bounty Research & Mastering Bugs

## Overview

On Day-13, I focused on studying a critical Remote Code Execution (RCE) vulnerability, reviewing a real-world bug bounty account takeover writeup, and continuing my Mastering Bugs initiative by documenting a Sensitive Data Exposure vulnerability.

---

## Tasks Completed

### 1. CVE Analysis

#### CVE-2024-20017 – Remote Code Execution in MediaTek Router Software

**Vulnerability Type:** Stack-Based Buffer Overflow (Remote Code Execution)

**Severity:** Critical

**Key Learnings:**

* Analyzed how insufficient bounds checking can lead to stack buffer overflows.
* Studied how attacker-controlled length values can overwrite adjacent memory regions.
* Learned different exploitation techniques including:

  * Return-Oriented Programming (ROP)
  * Jump-Oriented Programming (JOP)
  * Pointer Corruption
  * Arbitrary Memory Writes
* Explored how real-world router firmware vulnerabilities can lead to complete device compromise.
* Understood the impact of memory corruption vulnerabilities in embedded systems and network devices.

---

### 2. Bug Bounty Writeup Analysis

#### Account Takeover via Host Header Injection

**Vulnerability Type:** Host Header Injection

**Impact:** Account Takeover

**Key Learnings:**

* Studied password reset poisoning attacks.
* Learned how applications may incorrectly trust user-supplied Host headers.
* Understood how password reset links can be manipulated to leak reset tokens.
* Analyzed how an attacker can capture reset tokens and gain unauthorized access to victim accounts.
* Reviewed the complete attack flow from password reset request manipulation to full account takeover.

---

### 3. Mastering Bugs Progress

#### P4 – Sensitive Data Exposure via localStorage/sessionStorage

**Bug Category:** Sensitive Data Exposure

**Subcategory:** Sensitive Token Exposure

**Key Learnings:**

* Studied the risks of storing sensitive information inside browser storage.
* Learned why localStorage and sessionStorage should never contain passwords, tokens, or authentication secrets.
* Analyzed how Cross-Site Scripting (XSS) vulnerabilities can be used to extract sensitive data from browser storage.
* Documented attack scenarios, impact analysis, and remediation strategies.
* Added detailed notes and documentation to the **Mastering Bugs** folder.

---

## Skills Developed

* Remote Code Execution (RCE) Analysis
* Buffer Overflow Fundamentals
* Memory Corruption Concepts
* Host Header Injection Testing
* Password Reset Poisoning
* Account Takeover Analysis
* Sensitive Data Exposure Testing
* Browser Storage Security
* Vulnerability Documentation
* Root Cause Analysis

---

## Resources Used

* CVE-2024-20017 Technical Analysis
* Real-World Bug Bounty Writeups
* OWASP Web Security Testing Guide
* OWASP Secure Storage Guidelines
* Bug Bounty Learning Resources

---

## Mastering Bugs Update

Continued expanding the **Mastering Bugs** repository by documenting:

* Attack Scenarios
* Root Cause Analysis
* Impact Assessment
* Remediation Strategies

All detailed notes, writeups, and vulnerability documentation have been organized within the **Mastering Bugs** folder for future reference and revision.

---


