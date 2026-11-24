---
tags:
  - ksi
  - quizz
  - best-practices
  - design-patterns
---



**Context:** You have `GET /feed/:id` in `feed.routes.ts`. Feed items have a `status`, and an item that isn't `APPROVED` should only be visible to admins. You already use middlewares like `authenticate` and `requirePermission`.

**Question:** Where do you enforce "non-approved feed items are admin-only"? You're thinking it's a middleware job, but the middleware would have to load the row and then the service would load it again. Would you accept that double fetch, add a middleware that checks the response after the handler runs, or do something else?

> [!answer]- Answer
> You put it in the **service**. The middleware isn't where this rule lives, so there's no double fetch to worry about. The route only works out who the user is and passes that in:
>
> ```ts
> const isAdmin = allAdminRoles.some((role) => role === req.user.role);
> const data = await feedService.getById(id, { profileId: req.user.id, isAdmin });
> ```
>
> The service loads the row once and decides:
>
> ```ts
> const feed = await prisma.feed.findUnique({ where: { id }, include: feedInclude });
>
> const canView = feed && (feed.status === FeedStatus.APPROVED || viewer.isAdmin);
>
> if (!canView) throw new NotFoundException(`Feed with id ${id} not found`);
> ```
>## One Rule of thumb : If you're unsure about a check, put it in the service. A check missing from middleware usually only costs you a slower rejection. A check missing from the service is a security hole.
>
> When a non-admin asks for an unapproved item, you return **404, not 403**. That's the usual convention (GitHub does this for private repos), because a 403 tells them the item exists.
>
> General rule: middleware checks **who the user is**, and the service checks **what that user may see**.
>
> **Why:**
> - **Middleware that fetches the row:** the rule ends up split between middleware and service. The row is loaded twice, or passed along on `req`. Anything that calls `feedService` without going through this route (a list endpoint, a background job) skips the check.
> - **Middleware that checks the response:** the restricted data has already been loaded and is on its way out, so you're filtering at the last moment. Any endpoint that forgets this middleware leaks the data.
>
> **Principle:** request-level authorization (roles, logged in or not) goes in middleware. Resource-level authorization (depends on the row's fields) goes in the service, or a policy function the service calls.
>
> **When this doesn't apply:**
> - If the check depends only on the user, with no row data involved (for example, "admins only, period"), it's a middleware job.
> - If the item's existence isn't secret, a 403 with a clear message is fine.