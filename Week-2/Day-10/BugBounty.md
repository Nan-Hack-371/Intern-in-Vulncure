# AirDrop Privacy Weakness: Hash-Based Contact Discovery and Identity Recovery

## Overview

This research discusses a privacy weakness in Apple's AirDrop protocol that can allow an attacker to recover the identity of AirDrop users by reversing hashed identifiers.

The issue was originally discovered in 2019 by researchers from TU Darmstadt and later gained attention after reports suggested that Chinese authorities were using the technique to identify senders of AirDrop-transmitted materials.

---

# Affected Technology

## Product

* Apple AirDrop

## Component

* Contact Discovery Mechanism

## Discovery

Researchers from TU Darmstadt:

* Heinrich
* Hollick
* Schneider
* Stute
* Weinert

Research published at:

* USENIX Security 2021

---

# What is AirDrop?

AirDrop is Apple's peer-to-peer file-sharing protocol.

It allows users to share:

* Files
* Images
* Contacts
* Documents

using:

* Bluetooth
* Wi-Fi

---

## AirDrop Modes

### Contacts Only

```text
Only users in your contact list
can send files to you.
```

### Everyone

```text
Any nearby user
can send files.
```

---

# The Privacy Problem

For "Contacts Only" mode to work, AirDrop must determine whether the sender exists in the receiver's contact list.

To do this, Apple designed a discovery mechanism based on hashed identifiers.

---

# How AirDrop Discovery Works

## Step 1

Sender shares a truncated SHA-256 hash of:

```text
Email Address
or
Phone Number
```

Example:

```text
john@example.com
```

↓

```text
SHA-256 Hash
```

---

## Step 2

Receiver hashes all entries in its contact list.

```text
Contacts List
      ↓
Hash Every Contact
      ↓
Compare Hashes
```

---

## Step 3

If a match exists:

```text
Contact Found
      ↓
Accept AirDrop Request
```

Otherwise:

```text
No Match
      ↓
Reject Request
```

---

# Root Cause

## Weak Design Choice

Instead of using a cryptographically secure:

```text
Private Set Intersection (PSI)
```

protocol,

Apple chose a simpler:

```text
Hash Comparison
```

approach.

---

# Why This Is Dangerous

Hashes are not encryption.

If attackers can guess possible inputs, they can recover the original value.

This is called:

## Dictionary Attack

```text
Possible Email
      ↓
Hash It
      ↓
Compare
      ↓
Match Found
```

---

# Attack Flow

## Step 1

Obtain AirDrop hash.

```text
Victim
      ↓
AirDrop Discovery
      ↓
Hash Captured
```

---

## Step 2

Generate possible identifiers.

Examples:

```text
john@gmail.com
john123@gmail.com
john.doe@gmail.com
```

or

```text
Phone Numbers
```

---

## Step 3

Hash each candidate.

```text
Candidate
      ↓
SHA-256
      ↓
Compare
```

---

## Step 4

Find matching hash.

```text
Hash Match
      ↓
Identity Recovered
```

---

# Why Phone Numbers Are Easier

Phone numbers have a limited range.

Example:

```text
+1-000-000-0000
to
+1-999-999-9999
```

An attacker can generate every possible phone number and pre-compute hashes.

---

# Rainbow Table Attack

Instead of hashing every candidate repeatedly, attackers can create a database of:

```text
Identifier
      ↓
Hash
```

before the attack.

This database is called a:

## Rainbow Table

Benefits:

* Faster lookups
* Less computation during attack
* Scalable identification process

---

# Simple Example

Suppose AirDrop broadcasts:

```text
8f14e45fceea167a...
```

Attacker's database contains:

| Identifier                                    | Hash                |
| --------------------------------------------- | ------------------- |
| [john@example.com](mailto:john@example.com)   | 8f14e45fceea167a... |
| [alice@example.com](mailto:alice@example.com) | ...                 |
| [bob@example.com](mailto:bob@example.com)     | ...                 |

Result:

```text
Hash Match
      ↓
Identity Revealed
```

---

# Impact

## Privacy Risks

### User Identification

Attackers can identify:

* AirDrop senders
* AirDrop receivers

---

### Protest Tracking

Potential use cases include:

* Monitoring activists
* Identifying protesters
* Tracking anonymous communications

---

### Mass Surveillance

Large-scale collection of AirDrop identifiers may allow authorities to build databases linking users to devices.

---

# Why This Is Not Ideal

AirDrop attempts to provide privacy by hashing identifiers.

However:

```text
Hashing ≠ Anonymity
```

If identifiers are predictable, hashes can be reversed through guessing attacks.

---

# Recommended Solution

Researchers proposed replacing the current system with:

## Private Set Intersection (PSI)

A PSI protocol allows two parties to determine:

```text
Do we share a contact?
```

without revealing:

* Contact lists
* Phone numbers
* Email addresses

---

## Proposed Alternative

### PrivateDrop

Researchers developed:

```text
PrivateDrop
```

A Diffie-Hellman-based PSI protocol designed specifically for AirDrop-like contact discovery.

Benefits:

* Strong privacy
* No hash reversal attacks
* No identifier leakage

---

# Key Security Lessons

## What Developers Can Learn

### Do Not Invent Cryptography

```text
Custom Crypto
      ↓
Unexpected Weaknesses
```

---

### Hashing Is Not Encryption

```text
Sensitive Data
      ↓
Hashing
      ↓
Still Guessable
```

---

### Use Established Privacy Protocols

Examples:

* PSI
* Secure Multi-Party Computation
* Proven Cryptographic Designs

---

# Key Takeaways

* AirDrop uses hashed identifiers during contact discovery.
* Hashes can be reversed through dictionary and rainbow table attacks.
* Phone numbers are especially vulnerable because they are highly predictable.
* Researchers proposed PrivateDrop as a privacy-preserving replacement.
* The issue demonstrates why hashing alone is not sufficient for privacy protection.

---

# References

* TU Darmstadt AirDrop Research (2019)
* USENIX Security 2021 Paper
* Apple AirDrop Documentation
* PrivateDrop Research
* Rainbow Table Attack Research
* Dictionary Attack Research
