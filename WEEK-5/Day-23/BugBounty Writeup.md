# Hacking Microsoft and Wix with Keyboard Shortcuts

## Writeup Description

* The research demonstrates how browser behavior related to anchor tags (`<a>`) with the `target="_blank"` attribute can be abused to achieve Cross-Site Scripting (XSS).
* Normally, JavaScript protocol URLs (`javascript:`) are blocked when opened in a new tab using `target="_blank"`.
* However, browser keyboard shortcuts such as:

  * CTRL + Click
  * CTRL + ENTER
  * Mouse Middle Click
    can bypass this restriction and execute JavaScript code.
* The researcher combined this behavior with Clickjacking and iFrame techniques to create practical exploitation scenarios.
* Two real-world applications were affected:

  * Microsoft My Apps Portal
  * Wix Marketplace

---

## How It Affected

### Microsoft My Apps Portal

* API endpoint lacked server-side validation for bookmark URLs.
* Allowed users to save `javascript:` URLs as bookmarks.
* Resulted in Stored XSS within the user's account.
* Multiple Microsoft accounts could be logged in simultaneously under the same browser origin.
* Access tokens remained stored in localStorage.
* Successful exploitation exposed:

  * User profile information
  * Email data
  * Active Directory information
  * Microsoft Graph API resources
  * Organization-related data

### Wix Marketplace

* URL fields such as:

  * Demo Site URL
  * Terms & Conditions URL
    accepted JavaScript protocol URLs.
* Stored XSS payloads could be embedded in application listings.
* Preview URLs generated through JWT tokens could be shared with other users.
* Successful exploitation allowed arbitrary JavaScript execution in the Wix domain context.

---

## How It Was Exploited

### Browser Behavior Abuse

* Attacker inserts a malicious `javascript:` URL into a vulnerable input field.
* Browser blocks normal clicks because the link uses `target="_blank"`.
* Attacker embeds the vulnerable page inside an invisible iframe.
* CSS is used to align the malicious link with the victim's click position.
* Victim is instructed to:

  * Hold CTRL while clicking
  * Press CTRL + ENTER
  * Use middle mouse button

### Clickjacking Technique

* Vulnerable page is loaded inside an invisible iframe.
* Malicious website overlays the iframe.
* User interaction is redirected toward the hidden malicious link.
* Browser executes the JavaScript payload through keyboard shortcut behavior.

### Microsoft Attack Chain

1. Create malicious bookmark containing JavaScript payload.
2. Force victim to authenticate into attacker-controlled Microsoft account using SSO.
3. Load the vulnerable bookmark page.
4. Embed page inside iframe.
5. Trick victim into performing shortcut action.
6. Execute arbitrary JavaScript.
7. Extract access tokens from localStorage.
8. Access Microsoft Graph API resources.

### Wix Attack Chain

1. Create application listing containing JavaScript URL.
2. Generate preview link using JWT.
3. Share preview URL with victim.
4. Embed preview page in iframe.
5. Trick victim into CTRL-clicking.
6. Execute arbitrary JavaScript under Wix domain.

---

## Proof of Concept (PoC)

### Microsoft PoC

#### Step 1: Create Malicious Bookmark

```text
javascript:alert(document.domain)
```

#### Step 2: Store Bookmark

* Submit bookmark through the vulnerable API endpoint.
* Bookmark is stored without validation.

#### Step 3: Force Login

* Use attacker-controlled organization.
* Trigger SSO flow.
* Victim becomes authenticated to attacker's account.

#### Step 4: Embed Page

```html
<iframe src="https://myapps.microsoft.com"></iframe>
```

#### Step 5: Position Bookmark

```css
iframe {
  opacity: 0;
  position: absolute;
}
```

#### Step 6: Social Engineering

Victim is instructed to:

```text
Press CTRL + ENTER
```

or

```text
Hold CTRL and click anywhere
```

#### Result

```text
Arbitrary JavaScript executes.
```

#### Post Exploitation

* Read access tokens from localStorage.
* Access Microsoft Graph API.
* Extract organization and user information.

---

### Wix PoC

#### Step 1: Insert JavaScript URL

```text
javascript:alert(document.domain)
```

into:

* Demo Site URL
* Terms & Conditions URL

#### Step 2: Generate Preview Link

```http
POST /generate-preview
```

Returns:

```text
https://www.wix.com/market?appMarketParams=<JWT>
```

#### Step 3: Share Preview URL

* Send generated URL to victim.

#### Step 4: Load in Hidden iFrame

```html
<iframe src="https://www.wix.com/market?appMarketParams=<JWT>"></iframe>
```

#### Step 5: Trigger Shortcut

Victim performs:

```text
CTRL + Click
```

or

```text
CTRL + ENTER
```

#### Result

```text
JavaScript executes in the context of www.wix.com
```

---

## Security Impact

### Microsoft

* Stored XSS
* Token Theft
* Access to Microsoft Graph API
* Active Directory Enumeration
* Cross-Account Data Exposure

### Wix

* Stored XSS
* Arbitrary JavaScript Execution
* Session Theft
* User Data Exposure
* Account Compromise

---

## Key Takeaways

* Client-side restrictions should never replace server-side validation.
* `javascript:` URLs must be blocked on the server side.
* Clickjacking protections such as:

  * X-Frame-Options
  * CSP Frame-Ancestors
    should be implemented.
* Browser shortcut behavior can create unexpected attack vectors.
* Low-impact XSS findings may become critical when chained with other vulnerabilities.
