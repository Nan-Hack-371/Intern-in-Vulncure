# OAuth Misconfiguration to 0-Click Account Takeover (ATO)

This repository documents an Account Takeover (ATO) vulnerability arising from a critical implementation flaw in an OAuth 2.0 registration flow. 

Original Writeup:[here](https://medium.com/@mohamed0xmuslim/oauth-misconfiguration-leads-to-0-click-ato-b407fe05fdf4)

---

## Description
* The target application allows users to register and sign in using third-party OAuth providers (specifically Facebook).
* During a normal OAuth flow, user data such as First Name, Last Name, and Email are fetched from the social provider to automatically create or link the profile.
* However, if a user modifies their OAuth privacy settings to restrict sharing their email address, the application redirects them to a secondary registration page to manually complete their account profile.
* Due to a flaw in how the backend validates this secondary registration phase, an attacker can manipulate the submission to hijack any pre-existing account on the platform without requiring any victim interaction.

---

## Vulnerability
* **Broken Authentication & Logic Flaw:** The application blindly trusts the user-supplied email input on the registration fallback page.
* **Missing Server-Side Validation:** The application fails to re-verify whether the user actually owns the manually entered email address if that address is already marked as "verified" inside the database for an existing user.

---

## Vulnerable Component
* **Component:** OAuth Registration Fallback Endpoint / Registration Form API
* **Vulnerable Parameter:** `email` 

---

## Exploitation Technique
* **OAuth Data Restriction:** The attacker initiates registration via Facebook but blocks the application from accessing their Facebook email address.
* **Request Interception:** Because the email is missing, the application redirects the attacker to a fallback account creation form where the email input field is rendered blank/disabled on the UI.
* **Parameter Pollution / Injection:** The attacker intercepts the registration HTTP request and manually injects a victim's pre-registered and verified email address into the `email` parameter.
* **Authentication Bypass:** Because the injected email is already flagged as verified in the database, the backend skips the verification email check and directly logs the attacker into the victim's account session.

---

## Proof of Concept (PoC)
1. Initiate the registration flow using a Facebook account.
2. In the Facebook OAuth permissions dialog, uncheck/deny access to your **Email Address**.
3. You will be redirected to the target website's fallback `Create Account` page where the email field is empty.
4. Fill out a random username, click **Create Account**, and intercept the outgoing HTTP request using an interception proxy (e.g., Burp Suite).
5. Modify the POST request parameters as shown below:

```http
POST /api/v1/auth/register-oauth HTTP/1.1
Host: target.com
Content-Type: application/x-www-form-urlencoded

First_name=Attacker&Second_name=User&Username=attacker123&email=victim_verified_email@target.com
