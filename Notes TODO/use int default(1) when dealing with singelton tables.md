---
tags:
  - db
  - concept
  - ksi
  - tips-tricks
  - best-practices
---

---

- Using `id = 1` is an industry-standard pattern for singleton tables. It gives you the simplest code, avoids dealing with arbitrary UUIDs or strings, and guarantees that only record `#1` will ever exist.
- bonus tips: To make it an **enforced singleton** where only **one record can ever exist at the database level**, you can use The "Lock" Boolean Column, which is declaring a attribute `isOnly` that has a default of `true` and a `@unique` index.


```Code snippet
model SystemSetting {
  id     String  @id @default(uuid())
  isOnly Boolean @default(true) @unique

  principalStartDate DateTime? @db.Date
  principalEndDate   DateTime? @db.Date
  retakeStartDate    DateTime? @db.Date
  retakeEndDate      DateTime? @db.Date

  updatedAt DateTime @updatedAt
}
```

