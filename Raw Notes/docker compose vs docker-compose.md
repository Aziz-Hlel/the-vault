---
tags:
  - infra/docker
  - lessons-learned
---

---

- You should strongly recommend **`docker compose` (without the hyphen)**.
  
- ### Key Differences

	- **`docker compose` (Compose V2):** This is the modern, official standard written in Go. It runs directly as a plugin under the core `docker` command, providing better performance, faster execution, and active maintenance from Docker.
    
	- **`docker-compose` (Compose V1):** This was the standalone Python-based binary. It is officially **deprecated/end-of-life** by Docker and no longer receives updates or feature additions.