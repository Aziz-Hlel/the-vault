---
tags:
  - half-baked
  - languages/typescript/prisma
  - war-stories
  - db/postgress
---

---

- to be clear based on chat this problem is only concerning version prior to prisma@8
- prisma doesn't applies migration atomically, in other words if a line failed in the migration (line 7), all the previous lines (1 through 6) doesn't get reverted back automatically.
- we made a lot of research and fucking prisma doesn't provide any solution to this natively, no in the config not plugin, nada.
- the only half-baked solution would be to manually wrap migration files in a transaction like so : 
```sql
BEGIN;
/*
  Whatever code you have :
CREATE TYPE "EventType_new" AS ENUM ('PUBLIC_HOLIDAY', 'SCHOOL_HOLIDAY',);
ALTER TABLE "public"."event" ALTER COLUMN "type" DROP DEFAULT;
ALTER TYPE "EventType" RENAME TO "EventType_old";
*/
COMMIT;
```

- be aware wrapping indexation (thus unique too) in a transaction would raise an error so my advice would be always make indexation in a solo migration 