# Common SSRF Entry Points

## Overview

Before exploiting SSRF, the most important step is identifying functionality where the application makes requests on behalf of the user.

A simple question can help uncover SSRF opportunities:

> **"Can I make the server fetch something for me?"**

If the answer is yes, SSRF testing should begin.

---

# How SSRF Works

```text
User Input
    ↓
Application
    ↓
Server Makes Request
    ↓
Response Returned
```

Instead of the user making the request directly, the server makes the request.

If an attacker can control the destination URL, SSRF may be possible.

---

# Common SSRF Entry Points

## 1. URL Import Features

Applications often allow users to import content from external URLs.

### Examples

* Import image from URL
* Import document from URL
* Import PDF from URL
* Import RSS feed

### Example Input

```text
https://example.com/image.jpg
```

### Why It Is Interesting

The server downloads the resource.

If the URL can be changed, internal resources may become accessible.

### Test Payloads

```text
http://localhost
```

```text
http://127.0.0.1
```

---

# 2. Image Upload via URL

Many applications allow users to set profile pictures using external image URLs.

### Example

```text
Profile Picture URL:
https://example.com/avatar.png
```

### Backend Behavior

```text
Server Downloads Image
      ↓
Stores Image
      ↓
Displays Image
```

### SSRF Opportunity

Replace the image URL with:

```text
http://127.0.0.1
```

or

```text
http://localhost/admin
```

---

# 3. Webhooks

Webhooks are one of the most common SSRF entry points.

### Example

```text
Webhook URL:
https://example.com/webhook
```

Applications send HTTP requests to the provided URL.

---

## Why It's Dangerous

Attackers may force the server to interact with:

* Internal services
* Cloud metadata endpoints
* Localhost applications

---

## Test Payload

```text
http://127.0.0.1
```

---

# 4. URL Preview Features

Social media and messaging platforms often generate previews for submitted URLs.

### Example

```text
Paste a URL:
https://example.com
```

### Backend Behavior

```text
Server Fetches URL
      ↓
Extracts Metadata
      ↓
Generates Preview
```

### SSRF Potential

The server fetches attacker-controlled URLs.

---

# 5. PDF Generation

Applications frequently generate PDFs from URLs.

### Example

```text
Generate PDF From URL
```

User submits:

```text
https://example.com/report
```

Server visits the URL and creates a PDF.

---

## SSRF Opportunity

Replace with:

```text
http://localhost
```

or

```text
http://127.0.0.1/admin
```

---

# 6. Video Embedding Features

Applications may fetch metadata from:

* YouTube URLs
* Vimeo URLs
* External video platforms

### Example

```text
Video URL:
https://youtube.com/watch?v=XXXX
```

The server retrieves metadata.

Potential SSRF vector.

---

# 7. Feed Importers

RSS and Atom feed importers commonly perform server-side requests.

### Example

```text
Import Feed:
https://example.com/rss.xml
```

### SSRF Risk

Server fetches attacker-controlled URLs.

---

# 8. File Conversion Services

Some applications convert remote files.

### Examples

* URL to PDF
* URL to Image
* URL to DOCX

### Workflow

```text
User URL
    ↓
Server Downloads File
    ↓
Conversion Happens
```

This creates SSRF opportunities.

---

# 9. API Integrations

Many platforms connect to third-party services.

### Examples

* CRM integrations
* Payment systems
* Marketing tools
* Inventory management

### Potential SSRF Parameters

```text
url=
callback=
endpoint=
api=
feed=
```

---

# 10. OAuth and Callback URLs

Applications often require callback URLs.

### Example

```text
Redirect URI
Callback URL
Return URL
```

These should always be reviewed carefully.

---

# 11. Open Graph & Metadata Fetchers

Many websites automatically fetch:

* Page titles
* Descriptions
* Thumbnails

### Example

```text
https://example.com
```

The server requests the URL to retrieve metadata.

Potential SSRF vector.

---

# 12. XML-Based Features

Applications processing XML may fetch remote resources.

### Examples

* XML imports
* SOAP services
* XML parsers

These can sometimes lead to SSRF or XXE-based SSRF.

---

# High-Value SSRF Indicators

While testing, pay special attention to parameters such as:

```text
url=
uri=
path=
link=
image=
feed=
file=
document=
source=
dest=
destination=
redirect=
return=
callback=
api=
proxy=
```

---

# SSRF Testing Workflow

## Step 1: Identify Input

Look for:

* URL fields
* Import features
* Webhooks
* API integrations

---

## Step 2: Confirm Server Request

Use a valid URL first:

```text
https://example.com
```

Observe behavior.

---

## Step 3: Test Localhost

```text
http://127.0.0.1
```

```text
http://localhost
```

---

## Step 4: Test Internal Networks

```text
http://192.168.1.1
```

```text
http://10.0.0.1
```

```text
http://172.16.0.1
```

---

## Step 5: Check Response Differences

Look for:

* Different status codes
* Error messages
* Internal application names
* Response size changes
* New content

---

# SSRF Hunter Mindset

Instead of asking:

> "Where can I use SSRF payloads?"

Ask:

> "Where is the server making requests for me?"

The second question consistently leads to real-world SSRF discoveries.

---

# Quick Revision Cheat Sheet

## Common SSRF Entry Points

* URL Imports
* Image Imports
* Webhooks
* URL Previews
* PDF Generators
* Video Embeds
* RSS Feed Importers
* File Converters
* API Integrations
* OAuth Callbacks
* Metadata Fetchers
* XML Processing

## Common SSRF Targets

```text
127.0.0.1
localhost
192.168.x.x
10.x.x.x
172.16.x.x
169.254.169.254
```

## Key Question

```text
Can the server make a request
to a destination that I control?
```

If yes, SSRF testing should be a priority.
