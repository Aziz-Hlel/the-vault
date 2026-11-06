---
tags:
  - architecture
  - design-patterns
  - ksi
  - best-practices
  - design-principles
---

---


### Context

My auth module has a `JwtService` that handles JWTs:

```ts
signAccessToken(payload)
signRefreshToken(payload)
verifyAccessToken(token)
verifyRefreshToken(token)
```

I now need to save refresh tokens in Redis, so I can revoke them and detect reuse. I've created a `TokenStore` with `set`, `getdel` and `delete`.

### Question

Where should the logic of handling the `tokenStore`, can we add it to the `JwtService` since it's the one handling the jwt and why


### Answer

**Keep `JwtService` "dumb", and create a `SessionService` that combines the two.** It owns the flow: create the JWT, then save it in Redis.

```
AuthService      → checks credentials (register / login)
    ↓
SessionService   → owns the session rules (issue / rotate / revoke / revokeAll)
    ↓         ↓
JwtService    TokenStore
sign/verify   get/set/delete in Redis
```

```ts
class SessionService {
  async issue(accountId: string, role: AccountRole) {
    const tokenId = crypto.randomUUID();
    const refreshToken = jwtService.signRefreshToken({ id: accountId, tokenId });

    await tokenStore.store(accountId, refreshToken, tokenId);

    return {
      accessToken: jwtService.signAccessToken({ id: accountId, role }),
      refreshToken,
    };
  }
}
```

|Module|Knows|Doesn't know|
|---|---|---|
|`JwtService`|How to sign and verify tokens|That Redis or sessions exist|
|`TokenStore`|Redis keys, hashing, TTL|When to store or revoke a token|
|`SessionService`|The rules, such as "a refresh token works once"|How signing or Redis work internally|

### Why not put Redis in `JwtService`?

- **Unrelated code gets pulled in:** the auth middleware only needs `verifyAccessToken`, but it would still depend on Redis through its imports.
- **Tests get harder:** a pure `JwtService` needs only a secret, with no mocks. With Redis inside, even a simple signing test needs a real Redis or a mock.
- **Side effects get hidden:** a method like `issueTokenPair` sounds like it only creates strings, but it also writes to Redis.
- **The rules get scattered:** storing would happen in one file and revoking in another, so you'd have to read both to follow a token's lifecycle.
- **Changes spread:** moving from Redis to Postgres would mean editing the JWT code, which has nothing to do with storage.

### Principles behind it

- **Single Responsibility Principle:** each module has one reason to change.
- **Separation of concerns:** crypto, storage and session rules stay apart.
- **Dependency Inversion (next step):** pass the store into `SessionService` instead of importing it, so tests can use an in-memory fake:

```ts
class SessionService {
  constructor(private readonly store: TokenStore) {}
}

const realSessions = new SessionService(new RedisTokenStore(redis)); // production
const testSessions = new SessionService(new InMemoryTokenStore()); // tests
```

### Rule of thumb

Before adding a dependency to a class, ask: **"Does everyone who uses this class need this dependency?"** If not, it probably belongs one level up, in a module that coordinates the two.