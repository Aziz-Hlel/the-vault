---
tags:
  - db
  - concept
  - lessons-learned
  - ksi
---

---

- The feature **`NULLS NOT DISTINCT`**, introduced in **PostgreSQL 15**
-  In SQL, a unique constraint does not consider `NULL` to be equal to `NULL` (unless configured with `NULLS NOT DISTINCT`)
- meaning if you set a field as unique and also nullable, you can create various rows of that table with that field having the value null, 
- the `NULLS NOT DISTINCT` attribute can be configured at the **constraint/index level** too

```SQL
-- At the table constraint level:
ALTER TABLE evaluation 
ADD CONSTRAINT evaluation_unique_key 
UNIQUE NULLS NOT DISTINCT (studentId, teacherId, startDate, endDate);


-- At the field level :
CREATE TABLE profile (
    id SERIAL PRIMARY KEY,
    phone_number TEXT UNIQUE NULLS NOT DISTINCT
);

-- 1. Insert first row with phone_number = NULL -> SUCCEEDS
INSERT INTO profile (phone_number) VALUES (NULL);

-- 2. Insert second row with phone_number = NULL -> FAILS!
INSERT INTO profile (phone_number) VALUES (NULL);
-- ERROR: duplicate key value violates unique constraint "profile_phone_number_key"
-- DETAIL: Key (phone_number)=(null) already exists.
```