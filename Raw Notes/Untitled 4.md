




# Where does refresh token storage logic belong?

**Context:** You have a JWT service with `signAccessToken`, `signRefreshToken`, `verifyAccessToken` and `verifyRefreshToken`. You're adding Redis so refresh tokens can be rotated and revoked, and you've written a token store with `store`, `consume`, `revoke` and `revokeAll`.

**Question:** Login now has to sign a refresh token and save it to Redis. Where should the code that does both live?

> [!answer]- Answer
> You should create a session service that uses both, and keep the JWT service and token store unaware of each other.
>
> ```ts
> async issue(accountId, role) {
>   const tokenId = crypto.randomUUID();
>   const refreshToken = jwtService.signRefreshToken({ id: accountId, tokenId });
>   await tokenStoreService.store(accountId, refreshToken, tokenId);
>   return { accessToken: jwtService.signAccessToken({ id: accountId, role }), refreshToken };
> }
> ```
>
> General rule: when a workflow needs two components, put it in a layer above them instead of making one depend on the other.
>
> **Why:** If you put Redis in the JWT service, anything that only needs `verifyAccessToken`, like your authenticate middleware, pulls in a Redis connection through its imports. Your JWT service would also need Redis mocked in its tests. Kept pure, it needs no mocks, and session tests only have to fake the store.
>
> **Principle:** Single Responsibility Principle. The JWT service handles signing and verifying, the store handles persistence, and the session service handles the token lifecycle rules.
>
> **When this doesn't apply:** With purely stateless JWTs and no store, there's nothing to coordinate, so call the JWT service directly.
