---
tags:
  - infra/docker
  - best-practices
  - tips-tricks
  - vps
  - bash
---

---


- #### Alias Option (Optional)

	- If you have existing scripts or legacy projects hardcoded to use `docker-compose`, map an alias in your shell profile (`~/.bashrc`) so old scripts continue to work using the modern V2 backend:
	- ```Bash
		alias docker-compose="docker compose"
		```
	- run this command to restart bash : 
```Bash
source ~/.bashrc
```





