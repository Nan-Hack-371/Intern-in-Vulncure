# JWT Fundamentals & Cryptographic Basics

## Cryptography Basics

Cryptography is the practice of protecting information from unauthorized access and tampering.

### Encoding

Purpose:

* Convert data into another format for storage or transmission.

Examples:

* Base64
* URL Encoding
* Hex Encoding

Characteristics:

* Reversible
* No secret key required
* Provides no security

Example:

hello

↓

aGVsbG8=

Anyone can decode it back to "hello".

---

### Hashing

Purpose:

* Verify integrity of data.
* Securely store passwords.

Examples:

* MD5
* SHA1
* SHA256
* bcrypt
* Argon2

Characteristics:

* One-way function
* Cannot be reversed
* Same input produces same output

Example:

password123

↓

482c811da5d5b4bc6d497ffa98491e38

Uses:

* Password storage
* File integrity verification
* Digital signatures

---

### Encryption

Purpose:

* Protect confidentiality of data.

Characteristics:

* Uses a key
* Can be decrypted

Example:

Credit Card Number

↓

AES Encryption

↓

Ciphertext

Only someone with the correct key can decrypt it.

---

## Symmetric vs Asymmetric Encryption

### Symmetric Encryption

Same key is used for encryption and decryption.

Examples:

* AES
* DES

Process:

Plaintext
↓
Encrypt (Secret Key)
↓
Ciphertext
↓
Decrypt (Same Secret Key)
↓
Plaintext

Advantages:

* Fast

Disadvantages:

* Secure key sharing is difficult

---

### Asymmetric Encryption

Uses two keys:

* Public Key
* Private Key

Examples:

* RSA
* ECC

Process:

Public Key
↓
Encrypt
↓
Ciphertext
↓
Private Key
↓
Decrypt

Advantages:

* Safer key distribution

Disadvantages:

* Slower than symmetric encryption

---

# JSON Web Tokens (JWT)

JWT (JSON Web Token) is a standardized format for securely transferring claims between systems.

Common uses:

* Authentication
* Session Management
* Access Control

Unlike traditional sessions, JWT stores information on the client side.

---

## JWT Structure

A JWT consists of three parts:

Header.Payload.Signature
separated by dot
Example:
eyJraWQiOiI5MTM2ZGRiMy1jYjBhLTRhMTktYTA3ZS1lYWRmNWE0NGM4YjUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTY0ODAzNzE2NCwibmFtZSI6IkNhcmxvcyBNb250b3lhIiwic3ViIjoiY2FybG9zIiwicm9sZSI6ImJsb2dfYXV0aG9yIiwiZW1haWwiOiJjYXJsb3NAY2FybG9zLW1vbnRveWEubmV0IiwiaWF0IjoxNTE2MjM5MDIyfQ.SYZBPIBg2CRjXAJ8vCER0LA_ENjII1JakvNQoP-Hw6GG1zfl4JyngsZReIfqRvIAEi5L4HV0q7_9qGhQZvy9ZdxEJbwTxRs_6Lb-fZTDpW6lKYNdMyjw45_alSCZ1fypsMWz_2mTpQzil0lOtps5Ei_z7mM7M8gCwe_AGpI53JxduQOaB5HkT5gVrv9cKu9CsW5MS6ZbqYXpGyOG5ehoxqm8DL5tFYaW3lB50ELxi0KsuTKEbD0t5BCl0aCR2MBJWAbN-xeLwEenaqBiwPVvKixYleeDQiBEIylFdNNIMviKRgXiYuAvMziVPbwSgkZVHeEdF5MQP1Oe2Spac-6IfA

---

## JWT Header

Contains metadata about the token.

Example:

{
"alg": "HS256",
"typ": "JWT"
}

Fields:

alg = Signing Algorithm

typ = Token Type

---

## JWT Payload

Contains claims about the user.

Example:

{
"sub": "carlos",
"role": "blog_author",
"email": "[carlos@example.com](mailto:carlos@example.com)"
}

Common Claims:

sub = Subject/User

role = User Role

exp = Expiration Time

iat = Issued At

iss = Issuer

---

## JWT Signature

Purpose:

Protect the token from modification.

Server generates:

Header
+
Payload
+
Secret Key
↓
Hash Function
↓
Signature

Final Token:

Header.Payload.Signature

If payload changes:

role=user

↓

role=admin

The original signature becomes invalid.

---

# JWT vs Session Cookies

Traditional Session:

Browser
↓
Session ID
↓
Server Database

JWT:

Browser
↓
JWT
↓
Server

Server does not need to store session data.

---

# JWT Security Model

The server trusts the payload only if:

* Signature is valid
* Token is not expired
* Signing key remains secret

If any of these fail, the entire authentication mechanism is compromised.

---

# JWT Attacks

## 1. Accepting Arbitrary Signatures

Cause:

Developer uses decode() instead of verify().

Example:

decode(jwt)

instead of

verify(jwt)

example:
<img width="1329" height="320" alt="image" src="https://github.com/user-attachments/assets/699b971f-2b52-4be5-870a-275115609009" />

<img width="1341" height="443" alt="image" src="https://github.com/user-attachments/assets/bf4165ab-7146-41fd-a743-30144ac4a98b" />

Impact:

Attacker can modify claims freely.

Example:

{
"role":"user"
}

↓

{
"role":"admin"
}

---

## 2. Accepting Tokens with No Signature

Header:

{
"alg":"none"
}
Example:
<img width="1336" height="372" alt="image" src="https://github.com/user-attachments/assets/5a5b4031-7451-4a68-a23d-60a683be6a89" />

<img width="1333" height="366" alt="image" src="https://github.com/user-attachments/assets/f1b5ffb1-9bd3-4d2f-bf43-18c45a517329" />

Impact:

Server accepts unsigned tokens.

Attacker can forge arbitrary JWTs.

Note:

Modern applications usually reject this.

---

## 3. Brute-Forcing Secret Keys

Common in HS256 implementations.

Attack Steps:

Capture a valid JWT.
Identify the algorithm (HS256).

Use Hashcat to brute-force the secret:

hashcat -a 0 -m 16500 <jwt> <wordlist>
Recover the secret key.
Modify JWT claims (e.g., role=user → role=admin).
Re-sign the JWT using the recovered secret.
Send the forged token and gain elevated privileges.
Impact:

Attacker discovers signing key and generates valid JWTs.

---

# Why JWT Vulnerabilities Occur

JWT vulnerabilities are usually caused by:

* Missing signature verification
* Weak secret keys
* Incorrect library usage
* Misconfigured algorithms
* Trusting user-controlled data

Most JWT attacks do NOT break:

❌ AES

❌ RSA

❌ SHA256

Instead they exploit implementation mistakes.

---

# Real Pentester Checklist

When testing JWTs:

☐ Decode the token

☐ Inspect claims

☐ Identify signing algorithm

☐ Modify claims

☐ Test signature validation

☐ Check for alg:none

☐ Test weak secrets

☐ Check token expiration

☐ Review JWT headers

---

# Labs Completed

* What are JSON Web Tokens (JWTs)?
* What are JWT attacks?
* Impact of JWT attacks
* How vulnerabilities arise
* Working with JWTs in Burp Suite
* Exploiting flawed JWT signature verification
* Accepting arbitrary signatures
* Accepting tokens with no signature
* Brute-forcing secret keys
* Using Hashcat

---

# Key Takeaway

JWT security depends entirely on:

1. Proper signature verification.
2. Strong secret keys.
3. Secure implementation.

Most JWT vulnerabilities occur because applications trust modified tokens or use weak signing secrets.
