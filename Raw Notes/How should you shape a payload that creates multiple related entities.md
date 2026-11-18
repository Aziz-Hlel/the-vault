---
tags:
  - quizz
  - design-principles
  - ksi
  - lessons-learned
  - best-practices
---

---
# How should you shape a payload that creates multiple related entities?

**Context:** You have `POST /api/patients` — an admin endpoint that creates three separate DB records in one shot: a `User`, a `Profile`, and a `Patient`. You need to decide how to structure the request body.

**Question:** Which of these shapes would you go with, and why?

```json
// Option A — flat
{
  "email": "...", "password": "...",
  "firstName": "...", "gender": "...",
  "cancerType": "...", "cancerStage": "..."
}

// Option B — primary resource at root, parents nested
{
  "cancerType": "...", "cancerStage": "...",
  "user":    { "email": "...", "password": "..." },
  "profile": { "firstName": "...", "gender": "..." }
}

// Option C — fully nested by entity
{
  "user":    { "email": "...", "password": "..." },
  "profile": { "firstName": "...", "gender": "..." },
  "patient": { "cancerType": "...", "cancerStage": "..." }
}
```

> [!answer]- Answer
> **Option C.** Nest every entity under its own key, including the primary resource.
>
> **Why Option A fails:**
> - Field name collisions across entities become possible as schemas grow.
> - Validation errors lose their owner: `email is invalid` vs `user.email is invalid`.
> - Schemas can't be reused by composition — you're forced to merge and re-declare.
> - In Zod, Option C lets you compose existing schemas with zero duplication:
>   ```ts
>   z.object({
>     user:    registerSchema,
>     profile: createProfileReqSchema,
>     patient: createPatientReqSchema,
>   })
>   ```
>   And the service becomes a clean 1:1 passthrough:
>   ```ts
>   const user = await authService.register(data.user);
>   await profileService.create(user.id, data.profile);
>   return this.create(user.id, data.patient);
>   ```
>
> **Why Option B is tempting but wrong here:**
> Putting `cancerType` etc. at root feels justified because *this is the patient endpoint*. The comparison to `POST /orders` — where `items[]` often sits at root — is real, but the cases differ. `items[]` are a sub-collection *of the order itself*; they don't exist as separate top-level entities. `User`, `Profile`, and `Patient` are three distinct DB records. The URL already tells you this is a patient request; the body doesn't need to repeat it by promoting patient fields to root.
>
> **Principle:** Entity symmetry — each distinct record maps to exactly one key in the payload.
>
> **When this doesn't apply:** If the nested object is a sub-collection of the primary resource (e.g. `items[]` inside `POST /orders`), keeping it at root is idiomatic because it *belongs* to that resource, not beside it.