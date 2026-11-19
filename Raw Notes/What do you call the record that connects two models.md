---
tags:
  - architecture
  - design-patterns
  - best-practices
  - quizz
  - ksi
---

---

**Context:** You have a `Patient` model and a `Guardian` model. A guardian can be linked to several patients, and a patient can have several guardians. The row that connects them also holds its own data:

    model ??? {
      relationship          GuardianRelationship?
      linkStatus            GuardianLinkStatus @default(PENDING)
      canViewMedicalInfo    Boolean
      canManageAppointments Boolean
      canManageMedications  Boolean
      receivesNotifications Boolean
      guardianId String
      patientId  String
      @@unique([guardianId, patientId])
    }

You also have routes from both sides: `/patients/:patientId/guardians/...` and `/guardians/:guardianId/patients/...`.

**Question:** What do you name this model, and how do you name the request and response schemas built around it?

> [!answer]- Answer
> You should give it its own name from the domain, not glue the two table names together. Call it `Guardianship`, not `GuardianPatient`.
>
> Well-known examples of the same idea:
>
> | Glued name          | Domain name    |
> |---------------------|----------------|
> | `UserOrganization`  | `Membership`   |
> | `StudentCourse`     | `Enrollment`   |
> | `UserUser`          | `Friendship`   |
> | `GuardianPatient`   | `Guardianship` |
>
> Then you name every schema after that one resource, and add a point of view only where the response shape differs:
>
>
> A patient "has guardianships" and a guardian "has guardianships": it's the same record seen from either end.
>
> **General rule:** when a join table carries its own fields, it is an entity in its own right. Give it a noun from the domain and name everything around it after that noun.
>
> **Why:** if you keep `GuardianPatient`, the model's name is the same name your views want. Try naming the responses for three ordinary routes:
>
>     GET /guardian-patients/:id           → the link record itself     → GuardianPatientRes
>     GET /guardians/:guardianId/patients  → guardian sees his patients → GuardianPatientRes?
>     GET /patients/:patientId/guardians   → patient sees his guardians → PatientGuardianRes? GuardianPatientRes?
>
> Three different shapes are fighting over one name. You start inventing variants like `GuardianPatientDetailedRes` and `PatientGuardianRes`, and from the name alone you can't tell which is the raw record, which is the guardian's view, and which is the patient's. With a domain noun, each one gets its own name:
>
>     GET /guardianships/:id               → GuardianshipRes
>     GET /guardians/:guardianId/patients  → GuardianshipWithPatientRes
>     GET /patients/:patientId/guardians   → GuardianshipWithGuardianRes
>
> The noun names the thing, the suffix names the view, and the code uses the same words you'd use to describe the feature.
>
> **Principle:** Association class (a relationship that has its own attributes, modelled as a class of its own), plus Ubiquitous Language from DDD (names in the code match the words of the domain).
>
> **When this doesn't apply:** for a pure join table with no fields of its own (only the two foreign keys), a combined name like `_GuardianToPatient` is fine. Prisma even generates that for implicit many-to-many relations. Once the relation gets a status, permissions or timestamps, give it a real name.
