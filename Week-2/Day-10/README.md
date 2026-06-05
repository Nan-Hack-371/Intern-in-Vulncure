# Day 10 - Cyber Security Internship

## Overview

Today I focused on researching a P4 bug related to cryptographic weaknesses, analyzed a cryptography-related CVE, and studied a security research writeup involving Apple AirDrop's contact discovery mechanism.

---

# Tasks Completed

## 1. P4 Bug Research

### Vulnerability

**P4 | Cryptographic Weakness | Insufficient Entropy | Small Seed Space in Pseudo-Random Number Generator (PRNG)**

### Description

This vulnerability occurs when a pseudo-random number generator (PRNG) uses a small or predictable seed space.

Because the seed is not sufficiently random, attackers may be able to predict generated values such as:

* Session Tokens
* Password Reset Tokens
* API Keys
* Authentication Tokens
* Cryptographic Keys

### Example

```text
Weak Seed
    ↓
Predictable Random Numbers
    ↓
Predictable Security Tokens
    ↓
Potential Account Compromise
```

### Key Learning

Cryptographic security depends heavily on randomness. If random values can be predicted, security mechanisms built on them become vulnerable.

---

# 2. CVE Research

## CVE-2026-46344 - XMSS Buffer Overread

### Affected Software

* liboqs (Post-Quantum Cryptography Library)

### Vulnerability Type

* Out-of-Bounds Read
* Memory Safety Issue

### Root Cause

The XMSS signature verification function failed to validate the supplied signature length before processing it.

As a result:

```text
Short Signature Buffer
        ↓
Missing Length Validation
        ↓
Out-of-Bounds Read
        ↓
Application Crash
```

### Impact

* Denial of Service (DoS)
* Application Instability

### Key Learning

Even cryptographic libraries can become vulnerable if proper input validation is not implemented.

---

# 3. Security Research Writeup Analysis

## Apple AirDrop Privacy Weakness

### Topic Studied

Identity recovery through AirDrop's hash-based contact discovery mechanism.

### Main Issue

AirDrop uses hashed email addresses and phone numbers to determine whether users exist in each other's contact lists.

Researchers demonstrated that attackers may recover identities using:

* Dictionary Attacks
* Rainbow Table Attacks

### Attack Flow

```text
Hashed Identifier
        ↓
Dictionary / Rainbow Table Attack
        ↓
Hash Match
        ↓
Identity Recovery
```

### Key Learning

Hashing sensitive identifiers does not guarantee privacy when the original values are predictable.

---

# Resources Studied

* CVE-2026-46344 Advisory
* liboqs Security Advisory
* Apple AirDrop Research Paper
* Cryptographic Weakness Research Materials

---

# Today's Accomplishments

✅ Researched a P4 cryptographic weakness involving insufficient entropy in PRNGs

✅ Studied CVE-2026-46344 and analyzed its root cause and impact

✅ Analyzed an Apple AirDrop privacy-related security writeup

✅ Documented research findings in GitHub

---

# Notes

Progress on the following bug classes will continue to be documented separately in the **Week-2 Mastering Bugs** folder:

* SSRF
* XSS
* IDOR
* Cryptographic Failures
* Business Logic Vulnerabilities
