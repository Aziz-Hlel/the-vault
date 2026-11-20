---
tags:
  - design-principles
  - db/design
---




**Context:** You have a `Post` model, and your main feed query filters and sorts on four of its columns:

```prisma
model Post {
  id          String   @id @default(uuid())
  category    String
  isFeatured  Boolean  @default(false)
  publishedAt DateTime
  likeCount   Int      @default(0)
}
```

```sql
SELECT * FROM "Post"
WHERE category = 'news' AND "likeCount" >= 10
ORDER BY "isFeatured" DESC, "publishedAt" DESC
LIMIT 20;
```

**Question:** You want one composite index for this query. In what order do you list `category`, `isFeatured`, `publishedAt` and `likeCount`? And what happens to a query that doesn't filter by `category`?

> [!answer]- Answer
> You should index them like this:
>
> ```prisma
> @@index([category, isFeatured, publishedAt, likeCount])
> ```
>
> First the column you filter with `=`, then the `ORDER BY` columns in the same order as the query, then the column you filter with a range (`>`, `<`, `BETWEEN`). A query that doesn't filter by `category` mostly can't use this index.
>
> General rule: **equality first, then sort, then range.**
>
> **Why:** A B-tree index is sorted by its first column, then by the second within each value of the first, and so on.
> - `category` first: `= 'news'` jumps to one block of the index, and everything in that block is already ordered by the remaining columns.
> - `isFeatured, publishedAt` next: rows come out already in `ORDER BY` order, so Postgres reads 20 and stops, with no sort step.
> - `likeCount` last: it's checked on each row as it's read.
>
> The tempting mistake is to put `likeCount` right after `category` because it's also in the `WHERE`:
>
> ```prisma
> @@index([category, likeCount, isFeatured, publishedAt]) // range before sort
> ```
>
> Now rows within `news` are ordered by `likeCount`. The range `>= 10` covers many `likeCount` values, each with its own `isFeatured`/`publishedAt` ordering, so the combined result is no longer in `ORDER BY` order. Postgres has to read every matching row and sort them all before returning 20.
>
> The same idea explains the second part: the index is only useful for queries that filter on its leading column. Without `category`, there's no starting point, so Postgres mostly can't use this index and falls back to another index or a full scan. That's exactly why the column every query filters on goes first. If you also run queries without it (say, an admin "all posts by date" view), give those their own index, like `@@index([publishedAt])`.
>
> **Principle:** Equality-Sort-Range (ESR) rule, built on the leftmost-prefix rule of composite B-tree indexes.
>
> **When this doesn't apply:** On Postgres 18+, skip scan lets a query without `category` still use the index when `category` has only a few distinct values: Postgres runs the index once per value. It still won't hand you `ORDER BY publishedAt` across all categories without a sort, so the separate index is still worth it if that query matters.
