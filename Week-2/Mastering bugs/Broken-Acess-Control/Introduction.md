# Access Control Fundamentals

## Overview

Access Control is a security mechanism that determines:

* Who can access a resource
* What actions they can perform
* Which data they can view
* Which functionality they can use

It is one of the most critical security controls in any application.

When access control is improperly implemented, attackers may gain access to:

* Sensitive information
* Other users' accounts
* Administrative functionality
* Internal resources

According to OWASP, Broken Access Control is consistently one of the most common and impactful web application vulnerabilities.

---

# What is Access Control?

Access control answers a simple question:

```text
Should this user be allowed
to perform this action?
```

Every request made to an application should be validated before access is granted.

---

# Example

### User

```text
Regular User
```

### Allowed Actions

* View own profile
* Edit own account
* Change own password

### Restricted Actions

* Delete users
* View all customer data
* Modify user roles
* Access admin dashboard

If a regular user can perform restricted actions, an Access Control vulnerability exists.

---

# Why Access Control Matters

Without proper access control, attackers may:

* Read sensitive information
* Modify data
* Delete resources
* Take over accounts
* Gain administrative access

Even a single missing authorization check can lead to complete application compromise.

---

# Authentication vs Authorization

Many beginners confuse these concepts.

---

## Authentication

Authentication verifies:

```text
Who are you?
```

Examples:

* Username and Password
* OTP Verification
* Multi-Factor Authentication (MFA)

---

## Authorization

Authorization verifies:

```text
What are you allowed to do?
```

Examples:

* Can view profile
* Can edit profile
* Cannot access admin panel

---

## Example

User logs in successfully.

```text
Authentication ✓
```

User attempts to access:

```text
/admin
```

Application checks permissions.

```text
Authorization ✓ or ✗
```

A user can be authenticated but still unauthorized.

---

# Access Control Flow

```text
User Request
      ↓
Authentication Check
      ↓
Authorization Check
      ↓
Access Granted / Denied
```

---

# Types of Access Control Vulnerabilities

There are three major categories.

---

# 1. Vertical Privilege Escalation

## Definition

A lower-privileged user gains access to functionality intended for higher-privileged users.

### Example

```text
User
 ↓
Admin Functionality
```

### Example URL

```text
/admin
```

### Impact

* Administrative access
* User management
* System configuration changes

---

# 2. Horizontal Privilege Escalation

## Definition

A user gains access to another user's resources.

### Example

```text
User A
 ↓
User B Account
```

### Example URL

```text
/account?id=123
```

↓

```text
/account?id=124
```

### Impact

* Data theft
* Account compromise
* Privacy violations

---

# 3. Context-Dependent Access Control

## Definition

Access restrictions depend on application state or workflow.

### Example

```text
Step 1
Step 2
Step 3
```

An attacker skips:

```text
Step 1
Step 2
```

and directly accesses:

```text
Step 3
```

### Impact

* Unauthorized actions
* Workflow bypass
* Business process abuse

---

# Common Access Control Weaknesses

## Unprotected Functionality

Sensitive functionality exists but lacks authorization checks.

Example:

```text
/admin
```

---

## Hidden URLs

Developers rely on secrecy instead of security.

Example:

```text
/administrator-panel-x892
```

---

## Parameter Manipulation

Authorization depends on user-controlled parameters.

Examples:

```text
role=user
admin=false
isAdmin=0
```

---

## Insecure Direct Object References (IDOR)

Applications expose internal object references.

Examples:

```text
userId=100
orderId=500
invoiceId=700
```

---

## Workflow Bypass

Attackers skip required steps and directly access privileged functionality.

---

# Common Sources of Access Control Issues

### URL Parameters

```text
id=
userId=
accountId=
role=
```

---

### Cookies

```text
role=user
isAdmin=false
```

---

### HTTP Methods

```text
GET
POST
PUT
DELETE
```

---

### Request Headers

```text
Referer
X-Original-URL
X-Rewrite-URL
```

---

### API Endpoints

```text
/api/users/
/api/orders/
/api/admin/
```

---

# Access Control Testing Methodology

## Step 1

Create multiple accounts.

Example:

```text
User A
User B
Admin
```

---

## Step 2

Map all functionality.

Identify:

* Profile pages
* Admin pages
* APIs
* Account settings
* User management features

---

## Step 3

Capture requests.

Use:

```text
Burp Suite
```

to observe how the application handles authorization.

---

## Step 4

Test for:

* Vertical Privilege Escalation
* Horizontal Privilege Escalation
* IDOR
* Workflow Bypass
* Method-Based Bypass
* Header-Based Bypass

---

## Step 5

Compare responses.

Look for:

* Different data
* Status code changes
* Unauthorized functionality
* Sensitive information disclosure

---

# Real Bug Bounty Mindset

Don't ask:

```text
Can I access this page?
```

Ask:

```text
What authorization check
protects this page?
```

Then ask:

```text
Can I bypass that check?
```

This mindset is how most real-world Access Control vulnerabilities are discovered.

---

# Repository Roadmap

```text
Access-Control/
│
├── 01-Introduction-to-Access-Control
│
├── 02-Vertical-Privilege-Escalation
│
├── 03-Horizontal-Privilege-Escalation
│
├── 04-Access-Control-Bypass-Techniques
│
├── 05-Platform-Misconfigurations
│
├── 06-URL-Matching-Discrepancies
│
├── 07-Method-Based-Bypass
│
└── Labs
```

---

# Quick Revision Cheat Sheet

## Authentication

```text
Who are you?
```

---

## Authorization

```text
What are you allowed to do?
```

---

## Vertical Privilege Escalation

```text
User
 ↓
Admin
```

---

## Horizontal Privilege Escalation

```text
User A
 ↓
User B
```

---

## IDOR

```text
Change Identifier
 ↓
Access Another Resource
```

---

## Golden Rule

```text
Every request must verify
that the current user is
authorized to perform
the requested action.
```
