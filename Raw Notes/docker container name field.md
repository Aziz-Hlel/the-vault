---
tags:
  - infra/docker
  - ksi
  - concept
  - donts
---

---

- I asked chat which is better in general adding a container name to a service or docker or not
- In general, **not adding an explicit `container_name` is better and recommended for most Docker and Docker Compose workflows**.

### Why You Should Avoid `container_name`

1. **Breaks Scaling:**
    Docker Compose uses service names to auto-generate unique container names (e.g., `myapp-web-1`, `myapp-web-2`). If you define `container_name: my-web-app`, running `docker compose up --scale web=3` will fail because container names must be unique across your entire host.
2. **Name Collisions & Scope Conflicts:** 
    If two different Docker Compose files (or different project folders/environments on the same machine) use the same `container_name`, one will refuse to start because the name is already taken.
3.  **Prevents Zero-Downtime Deployments:**
    When Docker Compose updates or recreates a service, it often spins up a new container before stopping the old one. If a rigid `container_name` is set, Docker cannot create the replacement container until the old one is completely removed.
4.  **Service Names Already Handle DNS:** 
	You do **not** need a container name for containers to communicate with each other. In a Docker Compose network, services communicate using the **service name** (e.g., `http://db:5432` or `http://api:8080`), regardless of what the underlying container is named.
5.  **All previous logs are destroyed:** 
	**you keep `container_name: fairytopia-api-prod`**, running `docker compose up -d` will **destroy** the old container object completely to reuse the name. When Docker destroys the container, it deletes its `/var/lib/docker/containers/<id>` folder immediately—meaning the logs are erased from disk regardless of what you put in `daemon.json`.