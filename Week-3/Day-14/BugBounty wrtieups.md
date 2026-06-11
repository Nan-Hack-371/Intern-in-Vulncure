# HTTP Chunk Extension Processing Vulnerabilities

A comprehensive breakdown of the resource amplification and Denial of Service (DoS) vulnerability discovered across multiple major HTTP server libraries due to improper handling of HTTP chunk extensions.

original writeup:[here](https://nowotarski.info/http-chunk-extensions/)
---

## Description
* A family of vulnerabilities stemming from how major HTTP server libraries process HTTP/1.1 chunked transfer encoding metadata.
* According to RFC9112, chunks can include zero or more "chunk extensions" to supply per-chunk metadata (e.g., `[chunk size in hex];extension_name=extension_value\r\n`).
* The RFC mandates that servers must limit the total length of these extensions. However, several popular libraries failed to enforce these boundaries or enforced overly permissive limits.
* By abusing this behavior, an attacker can stream enormous amounts of data within the extension fields, resulting in severe resource amplification (network bandwidth and CPU exhaustion) or memory crashes.

---

## Vulnerability
* **Category 1 (No Limits):** Libraries that completely fail to track or restrict chunk extension sizes. Attackers can stream an infinite number of bytes, bypassing standard timeout configurations or maximum request body limits, eventually leading to Out-of-Memory (OOM) crashes or persistent CPU usage.
* **Category 2 (Permissive High Limits):** Libraries that enforce a limit per single chunk line but allow it to reset on every new chunk. By sending tiny (1-byte) data chunks paired with maximum-allowed extension sizes, an attacker can trick the server into reading gigabytes of data while appearing to stay within the "maximum read bytes" threshold for the unread request body.

---

## Vulnerable Component
* **Node.js (and llhttp):** (CVE-2024-22019) Internal HTTP parser `llhttp` lacked assignments for `on_chunk_extension_name` and `on_chunk_extension_value` callbacks, rendering Node.js entirely blind to extension lengths and allowing infinite reading.
* **Go (net/http):** (CVE-2023-39326) The `internal.chunkedReader` utilized a `bufio.Reader` buffer allowing up to 4,096 bytes per extension line. Combined with Go's mechanism to read/discard up to ~262 KB of unread request bodies to prevent client deadlocks, it created a massive multiplication loop.
* **Ruby on Rails (Puma):** (CVE-2024-21647) Vulnerable to chunk extension parsing amplification.
* **Rust (Hyper):** Affected HTTP library version prior to v1.1.0.
* **Legacy Nginx:** Versions prior to 1.19.2 were vulnerable (modern versions 1.24.0+ are secure).

---

## Exploitation Technique
* **Infinite Stream (Node.js):** Establish a connection and begin transmitting an HTTP/1.1 chunked request. Send the initial chunk size line followed by a semicolon and an unending stream of junk extension data (`1;ext=jjjjjjjjjjjj...`). Because callbacks are missing, the parser reads the stream indefinitely without triggering typical payload size constraints.
* **1-Byte Amplification Loop (Go):** Capitalize on the post-handler automatic discard rule (`io.CopyN(io.Discard, w.reqBody, maxPostHandlerReadBytes+1)`). Send a request body composed of 262,144 individual 1-byte chunks. For every 1 byte of actual body payload, attach a 4,096-byte chunk extension. 
* **The Amplification Math:** $$\text{Total Bytes Read} = 4,096 \text{ bytes (extension)} \times 262,144 \text{ chunks} = 1,073,741,824 \text{ bytes (1.07 GB)}$$
  The attacker uploads minimal data, but forces the backend server to read and discard over 1 GB of data per request, crashing performance.

---

## Proof of Concept (PoC)

### Concept Layout
An exploitation payload relies on structuring the raw HTTP/1.1 TCP stream to continuously inject large metadata fragments into the chunk length headers.

#### Node.js Infinite Bypass Pattern:
```http
POST / HTTP/1.1
Host: vulnerable-node-server
Transfer-Encoding: chunked

1;ext=AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA... [Stream continuously without terminating with \r\n]

###Go 1-Byte Chunk Multiplication Pattern:
HTTP
POST / HTTP/1.1
Host: vulnerable-go-server
Transfer-Encoding: chunked

1;ext=AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA... [Pad up to 4096 bytes]
X
1;ext=AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA... [Pad up to 4096 bytes]
X
[Repeat this sequence 262,144 times to trigger 1GB internal server amplification]
0
