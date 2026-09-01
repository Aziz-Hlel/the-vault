---
tags:
  - cloud
  - how-to
---

---

set up another user and dont use root user
ufw -> uncomplicated firewall : diable all ports and only leave 80,443 and ssh port, unnecessary if your cloud provider has a firewall management feature , which aws has 
change ssh port to 22 (debatable)
disable password login
prevent login via root user over ssh entirely 
install and configure unattended-upgrades to make automatic updates to your machine


[[Set up swap]]