---
tags:
  - cloud/aws/ec2
  - how-to
---

---

- A key pair, consisting of a private key and a public key,When you connect via SSH, your computer uses the **private key** to prove it matches the server's **public key**, granting access without sending a password over the network.


### Creating Key Pair 

#### Key Pair Type

- **Recommended: ED25519**
    
- **Why:** It offers stronger security with much shorter key lengths and faster cryptographic performance compared to RSA.
    

_Note: Use **RSA** only if you are using older operating systems that do not support ED25519 (such as Windows Server 2016 or legacy Linux distributions)._

#### Private Key File Format

- **Recommended: `.pem`**
    
- **Why:** Standard format native to macOS, Linux terminals, Windows OpenSSH (PowerShell/Command Prompt), and modern SSH clients like VS Code.