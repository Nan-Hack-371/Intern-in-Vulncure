# P4 - Sensitive Data Exposure via localStorage/sessionStorage

## Overview

This repository documents a Sensitive Data Exposure vulnerability where user credentials were stored in the browser's Web Storage mechanism.

The application saved sensitive authentication data, including usernames and passwords, directly into localStorage for analytics and tracking purposes. Since localStorage is fully accessible through JavaScript, this creates a significant security risk if an attacker is able to execute JavaScript within the application's context.

---

## Vulnerability Information

### Category

Sensitive Data Exposure

### Subcategory

Sensitive Token / Credential Exposure via localStorage

### Severity

P4

### Affected Component

* Browser localStorage (Web Storage API)
* Authentication Module
* Analytics/Tracking Module

### Impacted Data

* Username
* Password
* Authentication-related information

---

## Vulnerability Description

The application stored user credentials in plain text inside the browser's localStorage.

Unlike secure cookies that can be protected using the `HttpOnly` attribute, data stored in localStorage is accessible to any JavaScript executing within the application's origin.

As a result, if an attacker can execute JavaScript through a Cross-Site Scripting (XSS) vulnerability, they may be able to access and exfiltrate sensitive information stored within localStorage.

---

## Root Cause

The application incorrectly treated localStorage as a secure storage location for sensitive information.

Example:

```javascript
localStorage.setItem("account.username", username);
localStorage.setItem("account.password", password);
```

Sensitive authentication data should never be stored in client-side storage mechanisms that are accessible through JavaScript.

---

## Attack Scenario

### Step 1: XSS Vulnerability Exists

An attacker discovers a Cross-Site Scripting vulnerability in the application.

### Step 2: Victim Logs In

The application stores the victim's credentials inside localStorage.

### Step 3: Malicious JavaScript Executes

The attacker injects a script that reads localStorage.

Example:

```javascript
const data = localStorage.getItem("account.password");
```

### Step 4: Credential Theft

The malicious script sends the collected data to an attacker-controlled server.

### Result

The attacker gains access to sensitive user credentials.

---

## Impact

### Credential Disclosure

Usernames and passwords may be exposed.

### Account Compromise

Stolen credentials can be used to access user accounts.

### Privacy Risks

Sensitive information becomes available to unauthorized parties.

### Increased XSS Impact

Any XSS vulnerability becomes significantly more dangerous when credentials are stored in localStorage.

---

## Remediation

### Do Not Store Passwords

Passwords should never be stored in:

* localStorage
* sessionStorage
* IndexedDB

### Use Secure Session Management

Store session identifiers in:

* Secure Cookies
* HttpOnly Cookies
* SameSite Cookies

### Minimize Client-Side Storage

Only store non-sensitive information when absolutely necessary.

### Security Reviews

Review analytics and tracking implementations to ensure they do not collect authentication data.

---

## Key Learnings

* localStorage is not a secure storage mechanism for sensitive information.
* Any JavaScript running on the page can access localStorage.
* XSS vulnerabilities often become high-impact when sensitive data is stored client-side.
* Authentication secrets should be protected using secure cookie-based session management.
* Analytics systems should never log credentials or authentication data.

---

## Skills Practiced

* Sensitive Data Exposure Analysis
* Client-Side Security Review
* Authentication Security
* Web Storage Security
* XSS Impact Assessment
* Root Cause Analysis

---

## References

* OWASP Sensitive Data Exposure
* OWASP Session Management Cheat Sheet
* OWASP HTML5 Security Cheat Sheet
* Web Storage API Documentation
