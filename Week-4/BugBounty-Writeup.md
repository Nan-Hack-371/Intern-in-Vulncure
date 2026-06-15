🚀 Injecting Java In-Memory Payloads for Post-Exploitation

A security research repository documenting advanced Java post-exploitation techniques involving JVM instrumentation, memory-resident payloads, custom class loading, and in-memory persistence across enterprise Java applications.

📖 Description

Modern Java enterprise applications often run inside complex environments such as Tomcat, Jetty, Spring, Struts2, Jenkins, Bitbucket, and Confluence.

When attackers obtain Remote Code Execution (RCE), they may choose not to deploy traditional malware on disk. Instead, they can abuse JVM internals to inject malicious classes directly into memory, creating stealthy persistence mechanisms that are difficult to detect.

This repository analyzes several real-world attack paths and demonstrates how Java in-memory payloads can be leveraged during post-exploitation while also discussing defensive detection opportunities.

🎯 Objectives

This repository aims to:

Understand Java post-exploitation techniques.
Study JVM Instrumentation and Attach API abuse.
Analyze memory-resident persistence mechanisms.
Learn how attackers interact with application runtimes.
Explore defensive detection strategies.
Improve threat hunting capabilities for Java environments.
🏗️ Technologies Covered
Java
JVM
Spring Framework
Apache Tomcat
Jetty
Struts2
Groovy
Jenkins
Bitbucket
Confluence
⚠️ Vulnerable Components

The research focuses on applications where an attacker has already achieved code execution through a vulnerability.

1. Bitbucket Data Center
Vulnerability

CVE-2022-36804

Component
Git Archive Export Functionality
Root Cause
Command Injection
Unsanitized arguments passed to Git commands
Access Required
Public repository access
Result
Remote Code Execution (RCE)
2. Jenkins
Vulnerabilities
CVE-2017-1000353
CVE-2018-1000861
CVE-2019-1003005
CVE-2019-1003029
Components
Jenkins Remoting
Groovy Script Console
Pipeline Sandbox
Root Cause
Deserialization flaws
Sandbox bypasses
Arbitrary Groovy execution
Result
Controller compromise
Arbitrary code execution
3. Confluence Data Center
Vulnerability

CVE-2023-22527

Component
Velocity Template Engine
Root Cause
Server Side Template Injection (SSTI)
OGNL Expression Injection
Access Required
Unauthenticated
Result
Remote Code Execution (RCE)
🔥 Exploitation Techniques
JVM Attach API Abuse
Overview

The JVM Attach API allows one Java process to attach to another running JVM.

Attacker Goal
Inject Java Agents
Load arbitrary JAR files
Execute code inside trusted processes
Benefits
Fileless execution
Runtime access
Difficult detection
Instrumentation API Abuse
Overview

Java Instrumentation enables runtime inspection and modification of classes.

Capabilities
Load agents
Modify bytecode
Intercept application logic
Hook application functions
Dynamic Class Loading
Technique

Attackers create custom:

ClassLoaders
URLClassLoaders
ByteArrayClassLoaders
Purpose
Load malicious classes directly into memory
Avoid disk artifacts
Extend application functionality
In-Memory Webshell Injection
Overview

Instead of writing a webshell to disk, attackers register components directly inside the running application.

Common Targets
Servlet Filters
Tomcat Valves
Spring Controllers
Event Listeners
Advantages
No filesystem evidence
Difficult forensic recovery
Persistent until restart
Request Interception
Goal

Intercept all HTTP requests processed by the application.

Techniques
Custom Valve Registration
Filter Injection
Middleware Manipulation
Possible Outcomes
Session theft
Request monitoring
Credential harvesting
Runtime Framework Abuse
Spring Framework

Attackers may:

Access Beans
Execute internal services
Query application state
Examples
User services
Permission services
Authentication services
📸 Proof of Concept
PoC 1 – Java Agent Loading
Objective

Load a malicious Java agent into an already running JVM process.

Outcome
Agent executes inside target application.
Full runtime access obtained.
PoC 2 – Runtime Class Injection
Objective

Inject attacker-controlled classes.

Outcome
New functionality added without touching disk.
Application behavior modified dynamically.
PoC 3 – Memory-Resident Backdoor
Objective

Register malicious components directly in memory.

Outcome
Persistent access.
Difficult detection.
PoC 4 – Request Interception
Objective

Monitor incoming requests.

Outcome
Visibility into application traffic.
Potential credential exposure.
💥 Impact
Confidentiality Impact
High

Potential access to:

User accounts
Session tokens
Application secrets
Internal data
Integrity Impact
High

Attackers may:

Modify application behavior
Alter requests
Manipulate responses
Create unauthorized sessions
Availability Impact
Medium

Possible outcomes:

Service instability
Resource exhaustion
Application crashes
Persistence Impact
High

Memory-resident implants can:

Survive without disk artifacts
Remain hidden from traditional AV
Evade signature-based detection
🔍 Detection Opportunities
Process Monitoring

Monitor:

Runtime.exec()
ProcessBuilder
Unexpected child processes
JVM Monitoring

Look for:

Unknown Java Agents
Suspicious ClassLoaders
Dynamically loaded classes
Modified runtime behavior
Memory Analysis

Inspect:

Loaded classes
Active threads
Registered filters
Registered valves
Useful Tools
JCMD
JMAP
VisualVM
Eclipse MAT
Network Monitoring

Detect:

Unusual outbound traffic
Suspicious callbacks
Unknown HTTP endpoints
🛡️ Mitigation Strategies
Patch Vulnerabilities

Keep applications updated:

Product	Vulnerability
Bitbucket	CVE-2022-36804
Jenkins	Multiple RCEs
Confluence	CVE-2023-22527
Disable Unnecessary Features
Script Consoles
Debug Interfaces
Unused Admin Endpoints
Restrict JVM Attach API

Example:

-XX:+DisableAttachMechanism
Runtime Monitoring

Deploy:

EDR Solutions
JVM Monitoring Tools
Threat Hunting Workflows
Principle of Least Privilege

Applications should:

Run as non-root users
Restrict filesystem access
Limit outbound network access

References

Research Article
Synacktiv — Injecting Java In-Memory Payloads for Post-Exploitation
Related CVEs
CVE-2022-36804
CVE-2023-22527
CVE-2017-1000353
