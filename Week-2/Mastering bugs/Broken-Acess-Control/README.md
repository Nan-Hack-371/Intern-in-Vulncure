# 🔐 Access Control Mastery

> A structured learning repository for mastering Broken Access Control, Privilege Escalation, and IDOR vulnerabilities through theory, methodology, labs, and real-world examples.

---

# 📖 About This Repository

Access Control is one of the most critical security mechanisms in web applications. It determines:

* Who can access a resource
* What actions a user can perform
* What data a user can view
* What functionality a user can use

When these controls fail, attackers can gain unauthorized access to sensitive data, privileged functionality, and administrative features.

According to OWASP, **Broken Access Control** is consistently ranked among the most impactful web application security vulnerabilities.

This repository documents my journey toward mastering Access Control vulnerabilities for:

* Bug Bounty Hunting
* Penetration Testing
* Web Application Security
* CTFs
* Real-world Vulnerability Research

---

# 🎯 Learning Objectives

By completing this repository, I aim to:

* Understand how authorization works
* Identify Broken Access Control vulnerabilities
* Master Vertical Privilege Escalation
* Master Horizontal Privilege Escalation
* Discover and exploit IDOR vulnerabilities
* Learn common Access Control bypass techniques
* Practice through PortSwigger labs and real-world scenarios
* Develop a systematic methodology for testing authorization flaws

---

# 🔑 Authentication vs Authorization

Many beginners confuse Authentication and Authorization.

### Authentication

```text
Who Are You?
```

Examples:

* Login
* Multi-Factor Authentication
* Password Verification

---

### Authorization

```text
What Are You Allowed To Do?
```

Examples:

* Access Admin Dashboard
* View Another User's Data
* Delete Accounts
* Change User Roles

---

# 🚨 What is Broken Access Control?

Broken Access Control occurs when an application fails to properly enforce restrictions on authenticated users.

As a result, users can:

* Access unauthorized data
* Perform unauthorized actions
* Escalate privileges
* Access administrative functionality

---

## Example

A normal user should not access:

```text
/admin
```

If they can access it directly:

```text
User
  ↓
Admin Functionality
```

Then the application contains a Broken Access Control vulnerability.


---

# 📚 Topics Covered

## 01. Introduction to Access Control

* Authentication vs Authorization
* Access Control Fundamentals
* Authorization Models
* Security Principles

---

## 02. Vertical Privilege Escalation

Learn how attackers gain access to higher-privileged functionality.

Examples:

* Admin Panel Access
* Hidden Administrative Features
* Parameter-Based Access Control
* Security by Obscurity Failures

---

## 03. Horizontal Privilege Escalation

Learn how attackers access resources belonging to other users.

Examples:

* User Profile Access
* Order Access
* Invoice Access
* Document Access

---

## 04. Access Control Bypass Techniques

Common techniques used to bypass authorization controls.

Examples:

* Multi-Step Process Bypass
* Referer-Based Access Control Bypass
* Location-Based Access Control Bypass
* Workflow Manipulation

---

## 05. Platform Misconfigurations

Authorization failures caused by infrastructure or framework misconfigurations.

Examples:

* X-Original-URL
* X-Rewrite-URL
* Reverse Proxy Issues

---

## 06. URL Matching Discrepancies

Authorization bypasses caused by URL parsing inconsistencies.

Examples:

```text
/admin
/ADMIN
/admin/
/admin.anything
```

---

## 07. Method-Based Bypass

Testing authorization enforcement across HTTP methods.

Examples:

```http
POST /admin/deleteUser
```

vs

```http
GET /admin/deleteUser
```

---

# 🆔 Relationship Between Access Control and IDOR

One of the most common Access Control vulnerabilities is:

### Insecure Direct Object Reference (IDOR)

Example:

```text
/account?id=123
```

Changing:

```text
123 → 124
```

may expose another user's account if authorization checks are missing.

Most real-world IDOR vulnerabilities are actually examples of:

```text
Horizontal Privilege Escalation
```

---

# 🛠 Testing Methodology

Whenever testing Access Control, ask:

### Can I Access This Directly?

```text
/admin
/manage
/dashboard
```

---

### Can I Change User Identifiers?

```text
id=
userId=
accountId=
uuid=
guid=
```

---

### Can I Change HTTP Methods?

```http
GET
POST
PUT
DELETE
PATCH
```

---

### Can I Skip Workflow Steps?

```text
Step 1
Step 2
Step 3
```

Directly test:

```text
Step 3
```

---

### Can I Modify Headers?

Examples:

```http
X-Original-URL
X-Rewrite-URL
Referer
```

---

# 🧠 Bug Hunter Mindset

Most beginners ask:

```text
Can I access this page?
```

Experienced bug hunters ask:

```text
What authorization check should happen here?
```

Then they verify whether that check actually exists.

---

# 🎓 Learning Resources

* PortSwigger Web Security Academy
* OWASP Top 10
* Bug Bounty Reports
* Real-World Access Control Vulnerabilities
* CTF Challenges

---

# 🚀 Goal

The goal of this repository is not just to solve labs, but to build the mindset required to discover Access Control vulnerabilities in real-world applications.

A large percentage of critical bug bounty findings originate from one simple question:

```text
Should this user really be allowed
to perform this action?
```

If the answer is **No**, but the application allows it anyway, you may have discovered a Broken Access Control vulnerability.

---


