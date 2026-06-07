# 🔐 Cryptographic Failures

This folder contains my notes, practical labs, research, and learning progress related to **Cryptographic Failures (OWASP Top 10 - A02)**.

The goal of this section is to understand how cryptography is used in modern web applications, identify common implementation mistakes, and learn how attackers exploit these weaknesses in real-world environments.

---

## 📚 Topics Covered

### Cryptography Fundamentals

* Encoding vs Hashing vs Encryption
* Symmetric Encryption
* Asymmetric Encryption
* Digital Signatures
* Cryptographic Integrity
* Confidentiality and Authentication

### JWT (JSON Web Tokens)

* JWT Structure
* Header, Payload, Signature
* JWT Authentication
* JWT Signature Verification
* HS256 vs RS256
* JWT Security Model

### JWT Attacks

* Accepting Arbitrary Signatures
* Accepting Tokens with No Signature (`alg:none`)
* Weak Secret Keys
* Brute-Forcing JWT Secrets
* JWT Authentication Bypass
* JWT Misconfigurations

### Password Security

* Password Hashing
* Salting
* bcrypt
* Argon2
* Weak Hashing Algorithms
* Password Storage Best Practices

### Secure Randomness

* Predictable Tokens
* Weak Random Number Generation
* Password Reset Tokens
* Session Tokens
* OTP Security

### Session Security

* Session Cookies
* Secure Flag
* HttpOnly Flag
* SameSite Cookies
* Session Management

### Secrets Exposure

* Hardcoded Secrets
* API Key Disclosure
* JWT Secret Exposure
* Source Code Leaks
* Configuration Leaks

---

## 🎯 Key Learning Objectives

By completing this section, I aim to understand:

* How cryptography protects data
* How JWT authentication works
* How attackers exploit weak cryptographic implementations
* Why password storage mechanisms fail
* How weak randomness leads to account compromise
* How secrets exposure impacts application security
* How to identify cryptographic failures during penetration testing and bug bounty hunting

---
## 🛠️ Tools Used

* Burp Suite
* Hashcat
* CyberChef
* JWT.io
* Browser DevTools
* Kali Linux

---

### Learning Platforms

* PortSwigger Web Security Academy
* TryHackMe – Cryptographic Failures
* OWASP Web Security Testing Guide
* OWASP Cheat Sheets

### Documentation

* JWT.io Documentation
* Hashcat Documentation
* OWASP Password Storage Cheat Sheet
* OWASP Cryptographic Storage Cheat Sheet
* OWASP Session Management Cheat Sheet
* OWASP Secrets Management Cheat Sheet

### Practice Applications

* OWASP Juice Shop
* DVWA (Damn Vulnerable Web Application)
* WebGoat

---

## 🚀 Progress Status

* [x] Cryptography Basics
* [x] JWT Fundamentals
* [x] JWT Attacks
* [ ] Password Storage
* [ ] Insecure Randomness
* [ ] Session Security
* [ ] Secrets Exposure
* [ ] Advanced Cryptographic Attacks

---

## 📝 Notes

The focus of this section is not on breaking cryptographic algorithms such as AES, RSA, or SHA-256 directly. Instead, the emphasis is on understanding how insecure implementations, weak secrets, improper validation, and developer mistakes can lead to critical security vulnerabilities in modern applications.
