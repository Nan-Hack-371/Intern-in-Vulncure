# SAML Signature Validation Bypass Leading to Account Takeover

## Description

This vulnerability occurs when a Service Provider (SP) fails to properly validate the digital signature of incoming SAML assertions or responses. SAML (Security Assertion Markup Language) relies on cryptographic signatures to ensure that authentication data originates from a trusted Identity Provider (IdP) and has not been modified during transmission.

In the affected implementation, the application accepted modified or unsigned SAML responses without verifying their authenticity. As a result, an attacker could manipulate identity-related attributes within the SAML assertion and gain unauthorized access to privileged accounts.

---

## Vulnerability Type

* Authentication Bypass
* Improper Signature Validation
* Account Takeover
* Privilege Escalation
* Broken Authentication

**Severity:** Critical

---

## Vulnerable Component

### SAML Authentication Flow

Affected components include:

* SAML Response Processing
* Assertion Validation Logic
* Signature Verification Mechanism
* Identity Attribute Mapping

The application trusted user-controlled SAML assertions without enforcing strict validation of:

* XML Digital Signatures
* Assertion Integrity
* Identity Provider Authenticity
* User Attribute Consistency

---

## Root Cause

The primary security issue was the absence of proper signature verification during SAML response processing.

The Service Provider accepted modified SAML assertions even after:

* Signature removal
* Assertion tampering
* User attribute modification
* Identity changes

Because signature validation was not enforced, the application trusted attacker-controlled authentication data.

---

## Exploitation Technique

### Reconnaissance

During testing, a login portal utilizing SAML-based authentication was identified.

The authentication workflow involved exchanging SAML requests and responses between the Service Provider and Identity Provider.

### Analysis

Inspection of the authentication process revealed:

* SAML messages were transmitted through HTTP POST requests.
* User identity information was stored within SAML assertions.
* The application relied on assertion attributes for account creation and authorization.

### Security Testing

The assessment focused on determining whether the application properly validated SAML signatures.

The following security checks were performed:

1. Modification of SAML assertion attributes.
2. Testing acceptance of altered user identifiers.
3. Testing application behavior when signatures were removed.
4. Validation of response integrity enforcement.

### Observation

The application continued processing modified SAML assertions despite the absence of a valid signature.

As a result, arbitrary user information supplied within the assertion was trusted and used by the application.

---

## Impact

Successful exploitation may allow an attacker to:

* Bypass authentication mechanisms
* Create unauthorized accounts
* Impersonate legitimate users
* Escalate privileges
* Gain administrative access
* Compromise sensitive organizational resources

### Business Impact

* Unauthorized access to internal systems
* Exposure of confidential information
* Loss of trust in authentication infrastructure
* Regulatory and compliance violations
* Potential full compromise of SSO-enabled services

---

## Indicators of Vulnerability

Common signs that an application may be vulnerable include:

* Acceptance of unsigned SAML assertions
* Missing signature verification checks
* User-controlled attributes directly mapped to accounts
* Failure to validate trusted Identity Provider certificates
* Inadequate assertion integrity validation

---

## Mitigation

### Mandatory Signature Verification

* Verify all SAML response signatures.
* Reject unsigned assertions.
* Reject invalid signatures immediately.

### Validate Identity Provider Certificates

* Only trust pre-configured Identity Provider certificates.
* Rotate certificates securely.

### Assertion Validation

Validate:

* Issuer
* Audience Restriction
* Destination
* InResponseTo
* NotBefore
* NotOnOrAfter

### Principle of Least Privilege

* Avoid assigning privileged roles directly from untrusted attributes.
* Require additional authorization checks for administrative access.

### Security Testing

Regularly perform:

* SAML security assessments
* Authentication testing
* Assertion tampering validation
* Configuration reviews

---

## Detection Methods

Security teams can identify this issue through:

* SAML assertion tampering tests
* Signature validation testing
* SSO configuration reviews
* Authentication security audits
* Penetration testing

---

## CVSS Assessment

### Suggested CVSS v3.1

**Base Score:** 9.8 (Critical)

Vector:

AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H

---

## References

### Standards

* OASIS SAML 2.0 Specification
* XML Signature Syntax and Processing


### Related Categories

* Broken Authentication
* Security Misconfiguration
* Improper Access Control
* Identity and Access Management Vulnerabilities

---

## Key Takeaway

The security of SAML authentication depends heavily on strict signature verification. Any failure to validate signatures allows attackers to manipulate authentication assertions, potentially resulting in complete authentication bypass and administrative account compromise.
