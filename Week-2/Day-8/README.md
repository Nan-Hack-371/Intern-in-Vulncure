# DAY-08

## Activities Completed

### 1. CVE Research

* Studied **CVE-2023-24489 (Citrix ShareFile RCE)** from a security research article.
* Learned how multiple vulnerabilities can be chained together to achieve **Remote Code Execution (RCE)**.
* Understood the exploitation flow:

  * Cryptographic Validation Bypass
  * Path Traversal
  * Arbitrary File Upload
  * Remote Code Execution

### 2. Bug Bounty Write-up Analysis

* Read and analyzed a bug bounty write-up from **Pentester.land**.
* Studied the research methodology used by security researchers.
* Learned how source code review and application enumeration can lead to vulnerability discovery.
* Improved understanding of real-world vulnerability chaining.

### 3. Cryptography Learning

* Learned the difference between:

  * Encryption
  * Authentication
  * Integrity Verification

* Understood why:

> Encryption does not automatically provide authentication.

* Studied:

  * AES-CBC
  * PKCS#7 Padding
  * Cipher Block Chaining (CBC)
  * Padding Validation Issues

### 4. Vulnerability Research

* Learned how improper cryptographic validation can introduce security weaknesses.
* Studied the concept of padding-related cryptographic flaws.
* Learned how developers should use authenticated encryption such as AES-GCM instead of relying solely on encryption.

### 5. P4 Severity Bug Study

* Researched **P4 (Low Severity) vulnerabilities** using Google AI.

* Studied:

  * Stored XSS
  * Privileged User to No Privilege Elevation
  * Bugcrowd Vulnerability Rating Taxonomy (VRT)

* Learned:

  * Why some Stored XSS vulnerabilities receive a P4 rating.
  * How privilege levels affect bug severity.
  * How impact influences vulnerability classification.

### Key Takeaways

* Encryption ≠ Authentication.
* Vulnerability chaining often leads to higher impact than individual bugs.
* File upload functionalities are high-value attack surfaces.
* Severity depends on impact, privileges, and exploitability.
* Real-world bug bounty write-ups provide valuable insights into attacker methodology.

---

## Resources Used

* Pentester.land
* Assetnote Research Blog
* Google AI
* Public CVE References

---

## Time Spent

* CVE Research
* Bug Bounty Write-up Analysis
* P4 Severity Study
* Documentation & Notes Creation
