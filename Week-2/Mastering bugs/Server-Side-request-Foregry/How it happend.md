# Why Does SSRF Happen?

SSRF (Server-Side Request Forgery) happens because the server trusts user input too much and does not properly validate URLs before making requests.

## What Is the Main Weakness?

The main weakness is **Improper Input Validation**.

Developers sometimes create features where the server fetches data from a URL provided by the user, such as:

* Downloading an image from a URL
* Importing data from an external website
* Connecting to third-party APIs

The problem occurs when the application does not verify whether the supplied URL is safe.

## How Does SSRF Work?

1. The application asks the server to fetch data from a URL.
2. The attacker provides a malicious URL instead of a normal public URL.
3. The server trusts the input and sends the request.
4. The request is made from the server itself, not from the attacker.
5. Internal systems trust requests coming from the server.
6. Sensitive information may be returned to the attacker.

## Why Can the Server Access Internal Resources?

Most organizations have internal services that are not accessible from the internet, such as:

* Admin panels
* Databases
* Internal APIs
* Cloud metadata services

External users cannot access these resources directly.

However, the web server is usually allowed to communicate with them. If an attacker can control where the server sends requests, they can use the server as a proxy to reach internal systems.

## Simple Example

Imagine a company office with a security guard.

* The guard does not allow strangers inside.
* An employee can freely enter the building.
* A stranger tricks the employee into fetching confidential files from inside.

The stranger never enters the building, but still gets the files because the employee trusted the request.

In SSRF:

* The attacker = Stranger
* The server = Employee
* Internal systems = Secure office

## Developer Mistake

A common mistake is allowing the server to request any URL provided by the user.

For example:

❌ Bad: Allow requests to any URL

https://example.com/fetch?url=<user_input>

✅ Better: Only allow trusted domains

https://trusted-website.com

Developers should:

* Validate user-supplied URLs
* Block private and internal IP addresses
* Use allowlists (whitelists) for trusted domains
* Restrict access to internal network resources

## Summary

SSRF occurs when a server blindly trusts a user-provided URL and makes requests on the user's behalf. Attackers can abuse this behavior to access internal services, retrieve sensitive information, or interact with systems that are normally unreachable from the internet.
