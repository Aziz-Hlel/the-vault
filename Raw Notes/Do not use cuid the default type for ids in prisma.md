---
tags:
  - war-stories
  - donts
  - languages/typescript/prisma
---

---

- do not use cuid, when you declare a table with id in Prisma, ai or some documentation will incentivize you to do so , but fck them, Prisma and that shitty ass type, it has 2 versions , so the first is deprecated and exposed to security vulnerability, and nest (e.i class validator the default validator for it and angular if i m not mistaken) doesn't have a prebuilt function to validate it so just avoid it like the plague.