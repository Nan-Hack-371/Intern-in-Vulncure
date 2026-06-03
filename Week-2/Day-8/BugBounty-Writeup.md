# CVE-2023-24489

* **CVE:** CVE-2023-24489
* **Product:** Citrix ShareFile Storage Zones Controller
* **Vendor:** Citrix
* **Vulnerability Type:** Cryptographic Validation Bypass → Path Traversal → Arbitrary File Upload → Remote Code Execution (RCE)
* **Severity:** Critical
* **CVSS:** 9.8 (Critical)
* **Privileges Required:** None (Unauthenticated)
* **Attack Vector:** Network
* **Affected Component:** `/documentum/upload.aspx`
* **Research By:** Assetnote
* **Patched:** February 2023

---

# Vulnerability Overview

Citrix ShareFile Storage Zones Controller contained a critical vulnerability chain that allowed an unauthenticated attacker to upload arbitrary files and achieve Remote Code Execution (RCE).

The root cause was an insecure cryptographic validation mechanism where encrypted values were trusted without proper authentication or integrity verification.

---

# Root Cause Analysis

The vulnerability consisted of multiple issues:

### 1. Weak Authentication Check

The upload endpoint attempted to load a user session from a cookie.

If the cookie was missing, the application continued processing the request instead of rejecting it.

**Impact:**

* Upload functionality remained reachable without authentication.

---

### 2. Improper Encryption Validation

The application decrypted the `parentid` parameter using AES-CBC.

Instead of verifying authenticity, it only checked whether decryption returned a non-empty value.

**Problem:**

* Encryption was incorrectly treated as authentication.
* Valid padding was enough to bypass validation.

---

### 3. Path Traversal

The `uploadId` parameter was concatenated into file paths without sanitization.

Example:

```text
uploadid=x\..\..\..\cifs
```

This allowed attackers to write files outside the intended upload directory.

---

### 4. Arbitrary File Upload

Once validation was bypassed, attackers could upload arbitrary `.aspx` files into a web-accessible directory.

**Impact:**

* Server-side code execution.

---

# Attack Chain

```text
Unauthenticated User
        │
        ▼
AES Validation Bypass
        │
        ▼
Path Traversal
        │
        ▼
Arbitrary ASPX Upload
        │
        ▼
Remote Code Execution
```

---

# Discovery Process

The researchers:

1. Enumerated exposed `.aspx` endpoints.
2. Identified `/documentum/upload.aspx`.
3. Decompiled the application using dnSpy.
4. Analyzed upload logic.
5. Found weak AES validation.
6. Bypassed cryptographic checks using valid CBC padding.
7. Exploited path traversal.
8. Uploaded a malicious ASPX file.
9. Achieved remote code execution.

---

# Key Cryptographic Mistake

The application used:

* AES-CBC
* PKCS#7 Padding

When invalid padding was supplied:

```csharp
catch (Exception ex)
{
    ...
}
```

The decryption process failed.

Researchers discovered that providing ciphertext with valid padding could bypass the validation logic.

### Security Lesson

```text
Encryption ≠ Authentication
```

Encrypted data must also be authenticated using:

* AES-GCM
* Encrypt-then-MAC
* HMAC validation

---

# Proof of Concept Summary

### Step 1

Find a ciphertext value that produces valid padding.

### Step 2

Use the value as the `parentid` parameter.

### Step 3

Abuse path traversal through `uploadid`.

```text
x\..\..\..\cifs
```

### Step 4

Upload a malicious ASPX file.

### Step 5

Access the uploaded file through the browser.

### Result

```text
Remote Code Execution
```

---

# Impact

An attacker could:

* Upload arbitrary files
* Write files outside intended directories
* Execute server-side commands
* Fully compromise the ShareFile server
* Access sensitive customer data
* Establish persistence

---

# Remediation

### Cryptography

* Replace AES-CBC with AES-GCM.
* Use authenticated encryption.

### Input Validation

* Sanitize upload paths.
* Block path traversal sequences.

### File Upload Security

* Restrict executable uploads.
* Store uploads outside the webroot.

### Authentication

* Enforce authentication before processing uploads.

---

# Key Takeaways

* Encryption alone does not guarantee integrity.
* AES-CBC without authentication can introduce serious risks.
* File upload functionality is a high-value attack surface.
* Path traversal combined with arbitrary file upload often leads to RCE.
* Always use authenticated encryption such as AES-GCM.

---

# References

* Assetnote Research
* Citrix Security Advisory
* CVE-2023-24489
* CWE-22 (Path Traversal)
* CWE-434 (Unrestricted File Upload)
* CWE-502 (Improper Cryptographic Validation)
