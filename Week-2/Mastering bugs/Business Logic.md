# Business Logic Vulnerabilities - Notes

## Overview

Business Logic Vulnerabilities are security flaws that arise from incorrect design or implementation of an application's workflow, rules, or processes.

Unlike technical vulnerabilities such as SQL Injection or XSS, business logic flaws exploit the way an application is intended to function. Attackers abuse legitimate features in unexpected ways to achieve unauthorized actions.

These vulnerabilities are also known as:

* Application Logic Vulnerabilities
* Logic Flaws
* Workflow Vulnerabilities

---

## What is Business Logic?

Business logic refers to the rules and constraints that define how an application operates.

Examples:

* A user must pay before receiving a product.
* A coupon can only be used once.
* Only invited users can join an organization.
* Users can only modify their own accounts.
* A refund can only be requested after a purchase.

Business logic ensures that application processes follow intended workflows.

---

## What are Business Logic Vulnerabilities?

Business Logic Vulnerabilities occur when attackers manipulate application workflows to perform actions that developers never intended.

The application behaves according to its code, but the result violates the intended business rules.

### Example

Normal Workflow:

```text
Add Item → Checkout → Payment → Order Confirmed
```

Abused Workflow:

```text
Add Item → Skip Payment → Order Confirmed
```

The application allows an unintended state because validation is missing.

---

## Why Do Business Logic Vulnerabilities Occur?

### 1. Flawed Assumptions About Users

Developers often assume users will behave normally.

Example:

* User enters positive quantity values.
* User follows the correct workflow.
* User only uses the application through the browser interface.

Attackers intentionally violate these assumptions.

---

### 2. Missing Server-Side Validation

Applications sometimes rely on client-side controls.

Example:

```html
<input type="number" min="1">
```

Attackers can modify requests and send:

```text
quantity = -10
```

If server-side validation is missing, unexpected behavior may occur.

---

### 3. Complex Application Workflows

Large applications contain multiple interconnected features.

Developers may not fully understand how different components interact, leading to unintended security gaps.

---

### 4. Poor Documentation of Business Rules

When assumptions are not documented, developers may implement features inconsistently, creating security weaknesses.

---

## Common Business Logic Vulnerabilities

### Account Takeover via Invitation Abuse

An attacker modifies invitation-related parameters and gains access to another user's account.

---

### Price Manipulation

Changing product prices during checkout.

Example:

```text
Original Price: $100
Modified Price: $1
```

---

### Coupon Abuse

Using the same coupon multiple times when it should only be valid once.

---

### Authentication Workflow Bypass

Skipping steps in login or verification processes.

---

### Privilege Escalation

Accessing administrator functionality through workflow manipulation.

---

### Refund Abuse

Requesting refunds without satisfying the required conditions.

---

## Characteristics of Business Logic Flaws

* Exploit legitimate functionality.
* Often require manual testing.
* Difficult for automated scanners to detect.
* Highly application-specific.
* Usually involve workflow manipulation.
* May not contain coding errors.

---

## Impact of Business Logic Vulnerabilities

Impact depends on the affected functionality.

### Authentication Systems

May result in:

* Account Takeover
* Authentication Bypass
* Privilege Escalation

---

### Financial Systems

May result in:

* Fraud
* Unauthorized Discounts
* Free Purchases
* Financial Loss

---

### Business Operations

May result in:

* Service Abuse
* Reputation Damage
* Data Exposure
* Loss of Customer Trust

---

## Why Bug Bounty Hunters Love Business Logic Bugs

Business Logic Vulnerabilities are:

* Difficult to automate.
* Often missed during development.
* Unique to each application.
* Frequently rewarded with high severity ratings.

Many critical bug bounty reports involve business logic flaws rather than technical vulnerabilities.

---

## Testing Methodology

### Understand the Workflow

Ask:

* What is the intended process?
* What assumptions are being made?

---

### Identify User-Controlled Inputs

Look for:

* Prices
* Quantities
* User IDs
* Coupon Codes
* Account Identifiers

---

### Break Assumptions

Try:

* Negative values
* Extremely large values
* Skipping workflow steps
* Repeating actions multiple times
* Changing request parameters

---

### Analyze State Changes

Observe:

* Account status changes
* Payment status changes
* Role modifications
* Access control decisions

---

## Key Takeaways

* Business Logic Vulnerabilities exploit flawed workflows rather than coding mistakes.
* They occur when developers make incorrect assumptions about user behavior.
* Automated scanners often fail to detect them.
* Understanding application functionality is critical for finding these bugs.
* Many high-impact bug bounty findings are business logic vulnerabilities.
* Always test how an application behaves when users do something unexpected.

---

## References

* PortSwigger Web Security Academy
* OWASP Business Logic Vulnerabilities
* CWE-840: Business Logic Errors
* Real-World Bug Bounty Reports
