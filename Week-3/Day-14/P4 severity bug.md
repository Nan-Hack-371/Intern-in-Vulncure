OAuth Misconfiguration to 0-Click Account Takeover (ATO)
A summary of a critical vulnerability where an OAuth implementation flaw allowed full account takeover without user interaction by manipulating the registration request.

Original Writeup:[here](https://medium.com/@mohamed0xmuslim/oauth-misconfiguration-leads-to-0-click-ato-b407fe05fdf4)

📝 Description
The target application (an online marketplace) implemented a "Sign up with Facebook" OAuth feature. During the OAuth consent process, users have the ability to manually deselect or remove permissions, such as hiding their email address.

When a user hides their email, the application fails to handle the missing data securely. Instead of aborting the process or validating the email via a strict token verification check on the backend, it serves a registration form with an empty, disabled email field. Upon submission, the backend accepts any arbitrary email provided in the HTTP request body, matching it directly to existing accounts without verifying ownership.

🛑 Vulnerability
Insecure Request Processing / Improper Input Validation: The application blindly trusts the email identifier passed directly from the client-side POST body during the account creation phase, rather than relying strictly on verified attributes returned directly from the OAuth provider's secure token.

Lack of Identity Verification: The backend logs the session into an already registered, verified email address without requiring any password or secondary validation link.

⚙️ Vulnerable Component
Component: Registration / OAuth Callback Handler

Endpoint: The account creation API endpoint triggered post-OAuth redirection (e.g., /create-account or /register/oauth).

Parameter: email= parameter within the POST request body.

⚡ Exploitation Technique
OAuth Interception: The attacker initiates a "Sign up with Facebook" flow.

Permission Stripping: In the Facebook OAuth consent popup, the attacker edits permissions and deselects the email address.

Form Completion: The application redirects the attacker to a profile completion page where the email field is blank (and disabled via the UI).

Request Manipulation: The attacker populates a unique username, clicks "Create Account", and intercepts the outgoing HTTP request using a proxy tool (like Burp Suite).

Payload Injection: The attacker changes the empty email= parameter to the victim's registered email address.

Account Takeover: The backend processes the request, associates the OAuth token session with the victim's email, and logs the attacker into the victim's account instantly.

🧪 Proof of Concept (PoC)
Navigate to the registration page and click Sign up with Facebook.

Click "Edit Access" on the Facebook permission page and uncheck the Email address permission.

You will be redirected to the application's secondary setup page (email field will be empty).

Fill in the required username field and click Create Account.

Intercept the HTTP request:

HTTP
POST /signup/complete HTTP/1.1
Host: target-marketplace.com
Content-Type: application/x-www-form-urlencoded

First_name=Attacker&Second_name=User&Username=attacker123&email=
Modify the email parameter to match the victim's email address:

HTTP
POST /signup/complete HTTP/1.1
Host: target-marketplace.com
Content-Type: application/x-www-form-urlencoded

First_name=Attacker&Second_name=User&Username=attacker123&email=victim@target.com
Forward the request. The application responds with a valid session/cookie belonging to victim@target.com.
