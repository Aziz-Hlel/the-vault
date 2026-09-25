---
tags:
  - languages/typescript
---

---

``` ts
import z from 'zod';
import { prisma } from '@repo/db/prisma/browser';
import { PostMapper } from './posts.mapper';

export const postCursorSchema = z.object({
  cursorId: z.uuid().optional().catch(undefined),
  limit: z.coerce.number().int().positive().max(100).catch(10),
  userId:z.uuid(),
});

export type IPostCursor = z.infer<typeof postCursorSchema>;


const findByCursor = async (cursorParam: IPostCursor) => {
    const where = {
        userId: cursorParam.userId,
    }
    const query = prisma.post.findMany({
        where,
        include: {
          media: true,
        },
		cursor: cursorParam.cursor ? { id: cursorParam.cursor } : undefined,
        orderBy: { createdAt: 'desc' },
        take: cursorParam.limit,
      });

      const queryResponse = await query;

      const lastItem = queryResponse[cursorParam.limit];

      const nextCursor = lastItem?.id || null;

      const data = queryResponse.slice(0, cursorParam.limit);

      const dataResponse = transactionsData.map(ResourceMapper.toResponse);

      return {
        data : dataResponse ,
        nextCursor,
      };
};
```

