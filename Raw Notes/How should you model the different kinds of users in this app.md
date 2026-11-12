---
tags:
  - system-design
  - quizz
  - lessons-learned
  - ksi
  - architecture
  - db/design
---

---

**Context:** You're building an app that supports cancer patients, survivors and their families. It has these roles: `SYSTEM`, `SUPER_ADMIN`, `ADMIN`, `PATIENT`, `GUARDIAN`, `DOCTOR`. The system and admin roles need only basic login and personal info, and they don't play a big part in the app. Patients, guardians and doctors each have their own fields (a diagnosis, a guardian's link to a patient, a doctor's license and specialty) and their own relations (appointments, care teams).

**Question:** How do you design the database models for these users?

> [!answer]- Answer
> You should split them into three layers:
>
> ```
> User      → login only (email, password, oauth) + system role
> Profile   → personal data every human user has (firstName, lastName, gender, phone, birthday)
> Patient   ┐
> Guardian  ├→ role-specific data only, 1:1 with Profile
> Doctor    ┘
> ```
>
> Your roles fall into two different groups:
>
> | | Meaning | Where it lives |
> |---|---|---|
> | **System roles** | what you're allowed to do on the platform | `User.role`: `SYSTEM`, `SUPER_ADMIN`, `ADMIN`, `USER` |
> | **Domain roles** | who you are in the cancer journey | a `Patient`, `Guardian` or `Doctor` row |
>
> Admins get no model of their own. They're a `User` with an admin role, plus a `Profile` if the back office shows their name.
>
> ```prisma
> model Profile {
>   id        String @id @db.Uuid   // = User.id
>   firstName String
>   lastName  String
>
>   user     User      @relation(fields: [id], references: [id], onDelete: Cascade)
>   patient  Patient?
>   guardian Guardian?
>   doctor   Doctor?
> }
>
> model Patient {
>   id            String @id @db.Uuid  // = Profile.id
>   cancerType    CancerType?
>   diagnosisDate DateTime? @db.Date
>
>   profile Profile @relation(fields: [id], references: [id], onDelete: Cascade)
> }
> ```
>
> - **Shared primary key** (`Patient.id = Profile.id = User.id`). This guarantees 1:1, saves a join column, and lets you look up any role by the same id.
>
> **Why:**
> - **No duplicated fields.** Without `Profile` you'd repeat `firstName`, `lastName`, `gender`, `phone` and so on in Patient, Guardian and Doctor. Shared features like chat, notifications, a "who posted this" display or search would then need three-way `if` logic.
> - **Relations stay typed.** An `Appointment` points to `patientId → Patient` and `doctorId → Doctor`, so the reference itself says which role it expects. Things every user has, like messages or a community feed, point to `Profile`.
> - **One person can hold several roles.** A survivor can be a guardian for a family member, and a doctor can be a patient. With role tables that's just two rows on the same profile.
>
> **Principle:** Supertype/subtype modeling, also called Class Table Inheritance or table-per-type. Shared data goes in the parent table and each subtype adds its own table with the same primary key. Keep **system roles** (permissions) separate from **domain roles** (who the person is in the business).
>
> **When this doesn't apply:** If the roles have almost no fields or relations of their own, like your admins, a role enum is enough and you don't need extra tables.
>
> **General rule:** Store login in one place and shared personal data in one place. Give a role its own 1:1 table, using the same primary key, only when it brings its own data or relations.
