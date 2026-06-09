# Removing Linked Identity Does Not Invalidate Associated Sessions

## Overview

This repository documents a Session Management vulnerability discovered during bug bounty hunting on a private program.

The vulnerability occurred when an account's linked identity (secondary email linked through Google OAuth) was removed, but active sessions associated with that identity remained valid.

This allowed continued access to the victim's account even after the linked identity was disconnected.

---

## Vulnerability Classification

### Category

* Broken Authentication
* Session Management Vulnerability
* OAuth Misconfiguration
* Account Linking Flaw

### Severity

Medium

### CWE

* CWE-613: Insufficient Session Expiration
* CWE-384: Session Fixation
* CWE-287: Improper Authentication

---

## Vulnerable Workflow

### Normal Flow

1. User creates account using Primary Email.
2. User adds Secondary Email.
3. User links Secondary Email with Google OAuth.
4. User can access account through OAuth login.

### Expected Behavior

When:

* Secondary email is removed
* OAuth account is disconnected

All sessions associated with that identity should be invalidated immediately.

### Actual Behavior

Existing OAuth sessions remain active after unlinking.

The attacker continues to have access to the victim account.

---

## Attack Scenario

### Victim

1. Creates account using primary email.
2. Adds secondary email.
3. Links Google OAuth.

### Attacker

1. Obtains access to OAuth-authenticated session.
2. Maintains active session.

### Victim

1. Removes secondary email.
2. Disconnects Google account.

### Attacker

1. Refreshes existing session.
2. Session remains valid.
3. Continues modifying victim account.

---

## Impact

### Unauthorized Access

An attacker can continue accessing an account even after identity removal.

### Account Modification

The attacker can:

* Change profile information
* Modify account settings
* Re-link identities
* Potentially lock out legitimate users

### Privacy Risk

Sensitive account information remains exposed through stale sessions.

---

## Root Cause

The application fails to invalidate active sessions after:

* Identity unlinking
* OAuth disconnection
* Secondary email removal

Session lifecycle management is incomplete.

---

## Remediation

### Immediate Fixes

* Revoke all active sessions when linked identities are removed.
* Invalidate OAuth refresh tokens.
* Force reauthentication after identity changes.
* Audit all active sessions after account modifications.

### Additional Security Controls

* Session binding to account state.
* Risk-based authentication.
* Session revocation dashboard.
* Security event notifications.

---

## Lessons Learned

* Always test account linking features.
* Examine session behavior after account changes.
* Check whether sessions survive:

  * Email changes
  * Password changes
  * OAuth unlinking
  * MFA removal
* Session invalidation bugs often lead to impactful findings.

---

## References

* OWASP Session Management Cheat Sheet
* OWASP Authentication Cheat Sheet
* OAuth 2.0 Security Best Practices
* HackerOne Reports and Writeups
