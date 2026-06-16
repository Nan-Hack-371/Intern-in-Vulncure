# 🍎 AirDrop Privacy Vulnerability Analysis

## Hash-Based Contact Discovery & Identity Disclosure

> Security research repository analyzing privacy weaknesses in Apple's AirDrop protocol, including identity disclosure risks, hash-based contact matching flaws, dictionary attacks, rainbow table attacks, and the proposed PrivateDrop mitigation.

---

# 📖 Description

AirDrop is Apple's peer-to-peer file-sharing protocol that allows users to exchange:

* Photos
* Files
* Contacts
* Documents
* Other content

using Bluetooth and Wi-Fi.

To support **"Contacts Only"** mode, AirDrop must determine whether a sender exists in a recipient's contact list.

Instead of using a cryptographically secure **Private Set Intersection (PSI)** protocol, AirDrop historically relied on truncated **SHA-256 hashes** of:

* Phone Numbers
* Email Addresses
* Apple IDs

Research conducted by the Technical University of Darmstadt demonstrated that these hashed identifiers could be recovered through:

* Dictionary Attacks
* Rainbow Table Attacks

allowing adversaries to identify AirDrop users and potentially deanonymize senders.

Recent reports suggest these weaknesses have been leveraged to trace AirDrop users distributing information anonymously.

---

# 🎯 Vulnerability Overview

| Field                   | Value                              |
| ----------------------- | ---------------------------------- |
| Vulnerability Type      | Privacy Disclosure                 |
| Affected Technology     | Apple AirDrop                      |
| Attack Category         | Identity Disclosure                |
| Root Cause              | Insecure Contact Matching          |
| Attack Complexity       | Low-Medium                         |
| Authentication Required | No                                 |
| User Interaction        | Minimal                            |
| Impact                  | User Identification & Privacy Loss |

---

# ⚠️ Vulnerable Component

## Product

### Apple AirDrop

---

## Affected Feature

### Contacts Only Discovery Mechanism

AirDrop attempts to determine whether a sender exists within a recipient's contact list before allowing file transfers.

### Purpose

* Contact Validation
* Peer Discovery
* Transfer Authorization

---

## Root Cause

AirDrop transmits truncated SHA-256 hashes of:

* Phone Numbers
* Email Addresses
* Apple IDs

for contact matching purposes.

### Security Weaknesses

* Predictable Input Space
* Unsalted Hashes
* Offline Cracking Feasibility
* Rainbow Table Susceptibility
* Identity Disclosure Risk

---

# 🔬 Technical Background

## How AirDrop Discovery Works

### Step 1 — Sender Broadcast

The sender broadcasts hashed identifiers such as:

* Phone Numbers
* Email Addresses
* Apple IDs

---

### Step 2 — Contact Comparison

The receiving device:

* Hashes entries from its contact list
* Compares hashes against received values
* Determines whether a match exists

---

### Step 3 — Transfer Decision

#### Match Found

* Transfer request accepted

#### No Match Found

* Transfer request rejected

---

# 🔥 Exploitation Technique

## Phase 1 — Capture AirDrop Identifiers

### Objective

Collect hashed identifiers exchanged during AirDrop discovery.

### Sources

* Wireless Traffic
* Discovery Messages
* Device Logs

---

## Phase 2 — Build Candidate Database

### Target Identifiers

* Phone Numbers
* Email Addresses
* Apple IDs

### Data Sources

* Public Datasets
* Breached Databases
* Generated Number Ranges

---

## Phase 3 — Dictionary Attack

### Process

1. Generate candidate identifiers.
2. Hash each identifier.
3. Compare generated hashes with captured hashes.
4. Identify matching values.

### Result

* Recovery of user identities.

---

## Phase 4 — Rainbow Table Attack

### Objective

Accelerate identifier recovery.

### Process

* Precompute large volumes of hashes.
* Store optimized lookup chains.
* Resolve identifiers rapidly.

### Benefits

* Faster Lookups
* Reduced Computational Cost
* Large-Scale Tracking Capability

---

# 🧪 Attack Scenario

## User Discovery

### Attacker Goal

Identify anonymous AirDrop users.

### Attack Flow

1. Capture AirDrop discovery hashes.
2. Build lookup database.
3. Match identifiers.
4. Reveal sender identity.

### Result

* Loss of anonymity.

---

## Mass Tracking Scenario

### Potential Outcomes

An attacker may:

* Track Protest Participants
* Identify Activists
* Correlate Devices with Users
* Build Communication Profiles

---

# 💥 Impact

## Confidentiality Impact

### High

Potential exposure of:

* Phone Numbers
* Email Addresses
* Apple IDs
* User Identities

---

## Privacy Impact

### Critical

Attackers may:

* Deanonymize Users
* Track Communications
* Correlate Activities
* Monitor File-Sharing Behavior

---

## Organizational Impact

### High

Potential consequences include:

* User Surveillance
* Privacy Violations
* Reduced Freedom of Expression
* Increased Monitoring

---

## Long-Term Impact

### High

Recovered identifiers may be:

* Stored Permanently
* Correlated with Future Activity
* Used for Intelligence Collection

---

# 🔍 Detection Opportunities

## Wireless Monitoring

Monitor for:

* Excessive AirDrop Discovery Activity
* Repeated Identifier Collection Attempts

---

## Device Forensics

Inspect:

* AirDrop Logs
* Discovery Records
* Contact Matching Events

---

## Threat Hunting

Look for:

* Bulk Identifier Collection
* Automated Matching Systems
* Large Hash Databases

---

# 🛡️ Mitigation

## User Recommendations

### Disable AirDrop When Not Required

Benefits:

* Reduces Discovery Exposure
* Limits Identifier Leakage

---

### Limit Visibility

Use:

* Contacts Only Mode
* Short-Duration Sharing Sessions

---

### Reduce Identifier Predictability

Avoid predictable:

* Email Addresses
* Public Contact Information

---

## Vendor Recommendations

### Implement Private Set Intersection (PSI)

Benefits:

* Prevents Identifier Disclosure
* Reveals Only Contact Membership
* Preserves User Privacy

---

### Adopt PrivateDrop

Researchers proposed **PrivateDrop**, a PSI-based replacement for AirDrop discovery.

### Advantages

* Strong Privacy Guarantees
* Protection Against Offline Attacks
* Resistance to Rainbow Tables

---

### Reduce Logging

Avoid storing:

* Contact Hashes
* Discovery Metadata
* Sensitive Identifiers

---

# 📚 References

## Academic Research

### Technical University of Darmstadt

* AirDrop Privacy Analysis
* PrivateDrop: Practical Privacy-Preserving AirDrop

---

## Related Security Concepts

* Private Set Intersection (PSI)
* Dictionary Attacks
* Rainbow Tables
* SHA-256 Hashing
* Identity Disclosure Attacks

---

## Additional Resources

* USENIX Security 2021 Research Paper
* Apple AirDrop Documentation
* Privacy-Preserving Contact Discovery Research



---

# ⚠️ Disclaimer

This repository is intended for:

* Security Research
* Privacy Analysis
* Defensive Security Education
* Academic Study
* Threat Modeling

All research and testing should be conducted only in authorized environments and for educational purposes.
