---
tags:
  - infra/redis
  - lessons-learned
  - concept
---

---

- get and set are called string approach and hget and hset are a hash approach
- - **String Approach (`GET` / `SET`)** = Store individual **primitive values** (strings, numbers, Booleans, or JSON strings) under unique key names.
- - **Hash Approach (`HGET` / `HSET`)** = Store an actual **object / dictionary / key-value collection** under a single key name.



#### 1. String Approach (`GET` / `SET`)
- Think of this as creating standalone variable names in Redis:
```JavaScript
// What this looks like conceptually:
auth:refresh:user123:token1 = "active"
auth:refresh:user123:token2 = "active"
```

#### 2. Hash Approach (`HGET` / `HSET`)
- Think of this as storing a JavaScript Object or Python Dictionary:


```JavaScript
// What this looks like conceptually:
auth:user:user123:tokens = {
  "token1": "active",
  "token2": "active"
}
```

- **To check `token1`:** You run `HGET auth:user:user123:tokens token1`
- **Expiration:** You can set a TTL on the _entire object_ (`auth:user:user123:tokens`), but **you cannot set an expiration timer on individual keys inside the object**.


