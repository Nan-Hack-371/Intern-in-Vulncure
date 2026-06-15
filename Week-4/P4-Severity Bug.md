# From Exposed Error Log (P4) to Company Account Takeover (P1)

> Escalating an Information Disclosure vulnerability into unauthorized API access, sensitive data exposure, and potential account compromise.

Original Wrietup:[here](https://medium.com/@mohanad.hussam23/from-error-log-file-p4-to-company-account-takeover-p1-and-unauthorized-actions-on-api-35e45e43273a)
---

# 📌 Overview

During security testing of a target organization, an exposed log file revealed sensitive API authentication tokens and internal request information.

Although the initial issue appeared to be a low-severity information disclosure vulnerability, further investigation demonstrated how leaked credentials could be leveraged to access protected API endpoints, enumerate company assets, retrieve sensitive information, and perform unauthorized actions.

This report highlights the risks of improperly exposed logs and demonstrates how small security misconfigurations can lead to significant business impact.

---

# 🎯 Vulnerability Summary

| Field                   | Value                                                                |
| ----------------------- | -------------------------------------------------------------------- |
| Vulnerability Type      | Information Disclosure                                               |
| Initial Severity        | P4                                                                   |
| Escalated Severity      | P1                                                                   |
| Attack Vector           | Remote                                                               |
| Authentication Required | No                                                                   |
| User Interaction        | No                                                                   |
| Impact                  | Unauthorized API Access, Sensitive Data Exposure, Account Compromise |

---

# 🔍 Discovery Process

## Step 1 – Subdomain Enumeration

Multiple subdomain enumeration techniques were performed to identify potential attack surfaces.

### Tools Used

* Amass
* Subfinder
* Findomain
* Sublist3r

---

## Step 2 – Live Host Discovery

Live hosts were identified from the collected subdomains.

### Tools Used

* Httpx
* httprobe

---

## Step 3 – Directory Fuzzing

Directory enumeration was performed against discovered assets.

### Tools Used

* FFUF
* Dirsearch
* Wfuzz

---

# ⚠️ Vulnerable Component

## Publicly Accessible Log File

### Endpoint

```text
https://target-domain.com/log.txt
```

### Issue

The application exposed internal error logs to unauthenticated users.

### Security Risks

* Information Disclosure
* Credential Leakage
* Internal Endpoint Exposure
* API Enumeration

---

# 🔬 Root Cause

Sensitive information was being written into publicly accessible log files.

### Exposed Information

* Authorization Tokens
* Internal Headers
* API Requests
* Application Metadata
* Internal Endpoints

### Example Data Observed

```json
{
  "Authorization": "Token *************",
  "Accept": "application/json",
  "Content-Type": "application/json"
}
```

---

# ⛓️ Exploitation Chain

## Phase 1 – Log File Analysis

Reviewing the log file revealed:

* Authentication tokens
* API paths
* Request headers
* Internal service references

---

## Phase 2 – API Endpoint Discovery

Searching within the log file identified API endpoints.

### Example

```text
/api/encodingjobs/
/api/groups/
/api/devices/
/api/products/
```

---

## Phase 3 – Authentication Bypass

Accessing API endpoints directly returned:

```json
{
  "detail": "Authentication credentials were not provided."
}
```

The exposed token was then supplied in requests.

### Result

Successful authenticated API access.

---

## Phase 4 – API Enumeration

Additional endpoints were discovered through API fuzzing.

### Accessible Resources

#### Encoding Jobs

```text
/api/encodingjobs/
```

#### Devices

```text
/api/devices/
```

#### Groups

```text
/api/groups/
```

#### Products

```text
/api/products/
```

---

# 💥 Impact

## Unauthorized API Access

Attackers could interact with protected API endpoints without legitimate authorization.

---

## Sensitive Information Disclosure

Potential exposure of:

* Devices
* Groups
* Products
* Internal Records
* Customer Data

---

## Account Takeover Risk

If privileged tokens were exposed:

* Administrative actions may be performed
* Account settings could be modified
* New resources could be created

---

## Business Impact

### Confidentiality

High

Potential exposure of sensitive company information.

### Integrity

High

Unauthorized modification of resources.

### Availability

Medium

Potential disruption through misuse of API functionality.

---

# 🧪 Proof of Concept

## Step 1

Access exposed log file.

```text
https://target-domain.com/log.txt
```

---

## Step 2

Extract leaked API token.

```text
Authorization: Token *************
```

---

## Step 3

Identify API endpoints from logs.

```text
/api/encodingjobs/
/api/groups/
/api/devices/
/api/products/
```

---

## Step 4

Send authenticated requests using leaked credentials.

### Result

Protected resources became accessible.

---

# 🔍 Detection

Organizations should monitor for:

### Publicly Accessible Logs

* log.txt
* error.log
* debug.log
* stacktrace logs

---

### Token Leakage

Search logs for:

```text
Authorization:
Bearer
Token
API-Key
```

---

### Suspicious API Activity

Indicators include:

* Unusual API enumeration
* Large data retrieval
* Unexpected authenticated requests

---

# 🛡️ Mitigation

## Remove Public Access to Logs

Ensure logs are never exposed through web-accessible directories.

---

## Sanitize Sensitive Information

Never log:

* Authorization Tokens
* Session Cookies
* API Keys
* Credentials

---

## Implement Token Rotation

Immediately revoke exposed tokens and generate new credentials.

---

## Apply Least Privilege

API tokens should only have minimum required permissions.

---

## Monitoring and Alerting

Implement alerts for:

* Public file exposure
* Token leakage
* Excessive API requests

---

# 📚 References

## OWASP Resources

* OWASP API Security Top 10
* OWASP Sensitive Data Exposure
* OWASP Logging Cheat Sheet

## Related Vulnerability Classes

* Information Disclosure
* Credential Exposure
* Broken Access Control
* API Security Misconfiguration

---

# ⚠️ Disclaimer

This repository is intended for educational purposes, security research, and authorized security testing only. All testing should be performed only on systems where explicit permission has been granted.
