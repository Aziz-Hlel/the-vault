---
tags:
  - "#concept"
---

---


#### 1. `net.ipv4.tcp_syncookies = 1` (Protects Against SYN Floods)

- **The Problem:** When someone tries to establish a connection to your server via TCP, they send a `SYN` packet. Your server reserves memory for that connection and sends back a `SYN-ACK`. In a **SYN flood attack**, an attacker sends thousands of fake `SYN` requests per second without completing the connection, filling up your server's connection memory until legitimate users are blocked out.
    
- **The Fix:** Setting this to `1` enables **SYN Cookies**. When the connection queue fills up, your server stops reserving memory in advance. Instead, it embeds a cryptographic proof (a "cookie") inside the `SYN-ACK` sequence number. The connection memory is allocated _only_ after the client responds with a valid `ACK`.