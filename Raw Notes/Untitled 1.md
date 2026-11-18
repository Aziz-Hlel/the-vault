

# Calling `getById` at the end of `create` / `update`

## The pattern

A service method writes data, then returns the result by calling the same service's own read method:

```ts
updateGuardian = async (id: string, data: UpdateGuardianReq) => {
  // ...checks...
  await prisma.$transaction([
    prisma.user.update({ ... }),
    prisma.profile.update({ ... }),
    prisma.guardian.update({ where: { id }, data: {} }),
  ]);

  return this.getGuardianById(id); // re-read through the "get" use case
};
```

This is a common, widespread pattern (e.g. `return this.findOne(id)` in NestJS/Express services).

## Why people use it

- The update endpoint returns **exactly the same shape** as `GET /guardians/:id`.
- No duplicated `include` or mapping code.

## Downsides

**1. The update inherits the read's rules.**
`getGuardianById` is a *read use case*. If it later gains read-specific rules (hide deactivated guardians, viewer permission checks, extra filters), the update silently inherits them and can throw errors that make no sense for an update, e.g. a 404 right after a successful write.

**2. Race window between write and re-read.**
After the transaction commits and before `getGuardianById` runs, another request can modify or delete the guardian:
- modified → you return someone else's changes (usually harmless)
- deleted → the update succeeded but the client gets a **404** (misleading)

## Alternative: share the data shape, not the use case

Extract the `include` into a constant, and let the last write in the transaction return the data:

```ts
const guardianDetailsInclude = {
  profile: { include: { user: true, avatar: true } },
  guardianPatient: {
    include: { patient: { include: { profile: { include: { user: true, avatar: true } } } } },
  },
} satisfies Prisma.GuardianInclude;

updateGuardian = async (id: string, data: UpdateGuardianReq) => {
  // ...checks...
  const [, , guardian] = await prisma.$transaction([
    prisma.user.update({ ... }),
    prisma.profile.update({ ... }),
    prisma.guardian.update({ where: { id }, data: {}, include: guardianDetailsInclude }),
  ]);

  return GuardianMapper.toRes(guardian);
};

getGuardianById = async (id: string) => {
  const guardian = await prisma.guardian.findUnique({ where: { id }, include: guardianDetailsInclude });
  if (!guardian) throw new NotFoundException(`Guardian with id ${id} not found`);
  return GuardianMapper.toRes(guardian);
};
```

- Same response shape as GET (shared `include` + `GuardianMapper.toRes`).
- The update no longer depends on `getGuardianById`'s rules.
- The data comes back from the transaction, so there's no separate re-read step for another request to slip into.

## Rule of thumb

- `return this.getById(id)` is **fine** while `getById` is a plain "fetch + map + 404" helper.
- Once `getById` holds **read-specific rules**, stop calling it from writes.
- General rule: **one use case shouldn't call another. Share the lower-level pieces (query shape, mapper, repository function), not the use case's behavior.**
