# Day 18

## 📚 Learning Activities

### CVE Analysis

#### CVE-2023-42929 – macOS App Container Protection Bypass

* Studied how macOS application containers were protected before and after Sonoma.
* Learned how bundle identifier spoofing could allow unauthorized access to protected application container data.
* Analyzed Apple's introduction of the new TCC protection mechanism:

  * `kTCCServiceSystemPolicyAppData`
* Reviewed related bypass research and mitigation techniques.

---

### Privacy Vulnerability Research

#### AirDrop Identity Disclosure Vulnerability

* Studied privacy weaknesses in Apple's AirDrop contact discovery mechanism.
* Learned how hash-based contact matching could be abused using:

  * Dictionary Attacks
  * Rainbow Table Attacks
* Understood how attackers could potentially deanonymize AirDrop users.
* Explored the concept of Private Set Intersection (PSI) and the proposed PrivateDrop solution.

---

### Access Control Vulnerability Analysis

#### E-Notation Access Control Bypass

* Analyzed a critical Broken Access Control vulnerability caused by inconsistent numeric parsing.
* Learned how scientific notation (E-Notation) was used to bypass authorization checks.
* Understood how backend validation inconsistencies can lead to:

  * IDOR
  * Unauthorized Data Access
  * Exposure of Sensitive User Discussions

---

## 🛠️ Practical Work

### GitHub Repository Documentation

Created structured GitHub repositories for:

* AirDrop Privacy Vulnerability Analysis
* CVE-2023-42929 App Container Protection Bypass
* E-Notation Access Control Bypass


---

## 🎯 Bug Bounty Learning

* Continued studying real-world bug bounty writeups.
* Improved understanding of:

  * Broken Access Control
  * Information Disclosure
  * Privacy Vulnerabilities
  * Container Security
  * Authorization Bypass Techniques
* Practiced converting vulnerability reports into professional GitHub documentation.

---

## 📖 Key Takeaways

* Small validation inconsistencies can lead to critical access control vulnerabilities.
* Privacy vulnerabilities may not directly compromise systems but can have serious real-world consequences.
* Secure authorization checks must be enforced consistently across all backend services.
* Proper vulnerability documentation improves communication and understanding of security issues.
* Studying real-world CVEs and bug bounty reports helps build practical offensive and defensive security knowledge.
