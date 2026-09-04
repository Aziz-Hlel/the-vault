


In Docker Compose, the `restart:` field accepts these values:

```yaml
services:
  app:
    image: my-image
    restart: "no"           # Never restart (default)
```

```yaml
restart: always
```

- Always restart the container if it stops.
    
- Also restarts after Docker daemon restarts.
    

```yaml
restart: unless-stopped
```

- Restart unless you explicitly stopped the container.
    
- Common choice for production services.
    

```yaml
restart: on-failure
```

- Restart only when the container exits with a non-zero status code.
    

```yaml
restart: on-failure:5
```

- Restart on failure, but only up to 5 times.
    

Example:

```yaml
services:
  web:
    image: nginx
    restart: unless-stopped
```

For most long-running applications, `unless-stopped` is usually the preferred option.