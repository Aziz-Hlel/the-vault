---
tags:
  - quizz
  - architecture
  - best-practices
  - ksi
  - design-patterns/api
---




**Context:** You have a `feeling-log` module. Three roles can read feeling logs: a patient (only their own), a guardian (only patients they have an active guardianship with) and a super admin (anyone's). Only the patient writes.

**Question:** How do you structure the routes and the access checks? Three independent routes, one route with role checks inside, or something else?

> [!answer]- Answer
> You should expose one patient-scoped route set, shared by all three roles, and put the access decision in a single policy function called from middleware:
>
> ```ts
> router.get('/patients/:patientId/feeling-logs', authenticate, authorizeRead, feelingLogController.list);
> router.post('/patients/:patientId/feeling-logs', authenticate, authorizeWrite, feelingLogController.create);
>
> async function canReadPatientLogs(actor: AuthUser, patientId: string) {
>   if (actor.role === 'SUPER_ADMIN') return true;
>   if (actor.role === 'PATIENT') return actor.id === patientId;
>   if (actor.role === 'GUARDIAN') return guardianshipRepo.hasActive(actor.id, patientId);
>   return false;
> }
>
> const canWritePatientLogs = (actor: AuthUser, patientId: string) =>
>   actor.role === 'PATIENT' && actor.id === patientId;
> ```
>
> The service just query `{ patientId }`. They never know who is asking.
>
> **Why:** Three routes duplicate the controller, validation, pagination and serialization, and the copies drift apart. Every new role means new routes. Role `if`s inside the controller scatter the authorization rules across the code, so they are hard to test and easy to miss on one path.
>
> **Principle:** Resource-based authorization. The role says what kind of actor you are; a policy decides whether this actor may access this specific resource (this `patientId`). The route says what you want, the policy says whether you may have it.
>
> **When this doesn't apply:** If the super admin needs a cross-patient list (all patients, with filters), that is a different query, so give it its own admin-only route instead of bending the patient-scoped one.
>
> **General rule:** Don't create one endpoint per role. Expose the resource once, and decide access per actor and per resource in one place.