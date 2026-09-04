---
tags:
  - cloud
  - bash
  - vps
  - how-to
---

---

- ``` bash
	# Create a 2GB swap file
	sudo fallocate -l 2G /swapfile
	sudo chmod 600 /swapfile
	sudo mkswap /swapfile
	sudo swapon /swapfile
	
	# Make swap permanent across reboots
	echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
	
	# Lower swappiness from default(60%) so system prefers physical RAM
	sudo sysctl vm.swappiness=10
	echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

- ran these commands to check if all went correctly :
- ```bash
  sysctl vm.swappiness
  sysctl vm.swappiness
  ```
  