---
tags:
  - concept
---
---

#### 2. `net.ipv4.conf.all.rp_filter = 1` & `net.ipv4.conf.default.rp_filter = 1` (Protects Against IP Spoofing)

- **The Problem:** Attackers often forge (spoof) the sender's IP address on incoming packets to hide their identity or perform reflection attacks.
    
- **The Fix:** `rp_filter` stands for **Reverse Path Filtering**. Setting this to `1` enables "strict mode." When a packet arrives on a network interface, the Linux kernel checks its routing table: _"If I were to send a response back to this source IP, would I send it out through the same network interface it just arrived on?"_ If the answer is no, the kernel assumes the sender's IP is fake and drops the packet instantly.
    
    - `.all.` applies this rule to all existing network interfaces.
        
    - `.default.` applies this rule to any new network interfaces created in the future (like VPN or container interfaces).