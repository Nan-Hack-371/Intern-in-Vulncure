Access Control Mastery
Overview

Access Control is one of the most critical security mechanisms in modern web applications.

It determines:

Who can access a resource
What actions a user can perform
Which data a user can view
Which functionality a user can use

When access controls are improperly implemented, attackers can gain unauthorized access to data, functionality, or administrative features.

According to OWASP, Broken Access Control consistently ranks among the most severe and impactful web application vulnerabilities.

What is Access Control?

Access Control is the process of enforcing restrictions on authenticated users.

Simply put:

Authentication
=
Who Are You?

Authorization
=
What Are You Allowed To Do?

Many applications correctly verify a user's identity but fail to properly verify what that user is allowed to access.

Example

Imagine an application with three user roles:

User
Moderator
Administrator

Each role should have different permissions.

User

Can:

View own profile
Update own information
Create support tickets

Cannot:

Delete users
Manage roles
Access admin dashboard
Administrator

Can:

Manage users
Delete accounts
Access sensitive data
Modify permissions
Access Control Failure

If a normal user accesses:

/admin

and gains administrative functionality,

then the application suffers from Broken Access Control.

Why Access Control Matters

Broken Access Control often leads to:

Account takeover
Data exposure
Privilege escalation
Financial fraud
Administrative compromise
Full application takeover

Unlike many vulnerabilities, access control flaws frequently require no advanced payloads.

Often a simple request modification is enough.

Types of Access Control Vulnerabilities
1. Vertical Privilege Escalation

A low-privileged user gains access to functionality reserved for higher-privileged users.

Example
User
  ↓
Administrator

Example:

/admin

becomes accessible to a normal user.

2. Horizontal Privilege Escalation

A user gains access to another user's resources.

Example

User A accesses:

/account?id=123

Then changes:

123 → 124

and accesses User B's account.

3. Insecure Direct Object References (IDOR)

A specific form of access control vulnerability where user-controlled identifiers are used to access resources without proper authorization checks.

Example
/invoice?id=500

↓

/invoice?id=501
4. Multi-Step Process Bypass

Applications often protect some workflow steps but forget others.

Example
Step 1 Protected
Step 2 Protected
Step 3 Unprotected

Attackers directly access the final step.

5. Platform Misconfiguration

Authorization mechanisms fail due to framework or infrastructure misconfiguration.

Examples:

X-Original-URL bypass
X-Rewrite-URL bypass
Reverse proxy misconfigurations
6. URL Matching Discrepancies

Access controls and applications interpret URLs differently.

Examples:

/admin/deleteUser

↓

/ADMIN/deleteUser

↓

/admin/deleteUser/
7. Method-Based Access Control Bypass

An application blocks one HTTP method but allows another.

Example

Blocked:

POST /admin/deleteUser

Allowed:

GET /admin/deleteUser
Core Access Control Principles

A secure application should always enforce:

Principle of Least Privilege

Users should only receive the permissions necessary to perform their tasks.

Minimum Access
=
Reduced Risk
Deny By Default

If access is not explicitly allowed:

DENY
Server-Side Enforcement

Authorization decisions must occur on the server.

Never trust:

Hidden fields
Cookies
URL parameters
JavaScript logic
Client-side controls
Common Places to Test

During bug bounty hunting, pay special attention to:

User Profiles
/profile
/account
/settings
Administrative Panels
/admin
/manage
/dashboard
APIs
/api/users/
/api/orders/
/api/documents/
Financial Features
/payments
/invoices
/orders
File Management
/download
/uploads
/documents
