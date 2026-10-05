




- apparently you cannot override the default return type of response.json in express which is name ResBody which equate to any ik stupid
- the second best thing is to declare new type, a function name success and fail if you want  , has a more typed and defined params , then add a middleware that adds those two functions based on the declared type, which they the one who returns a res.json , a bonus is you can an Eslint rule to forbid using the res.json, theses tips are in the conversation : https://claude.ai/chat/d01262d1-be77-4f60-872b-f58ce91654a8
- Another extra tip is that you can annotate the json function with deprecated, that way anyone invoking it will get the function strike through, p.s : i tried looking for other annotation that might be even more helpful and or trying to overriding the type of json but with no vail so no need to go down that rabbit hole again, the way you implement the deprecation is : 

```TypeScript
// src/types/express.d.ts
import * as express from 'express-serve-static-core';

declare global {
  namespace Express {
    interface Response {
      /**
       * Disabled: Use custom response helper instead of .json()
       * @deprecated
       */
      json?: never;
    }
  }
}
```
- Another way is to declare a new type TypeResponse for example and use eslint , but you ll have to delcare each function in the controller with it like in this convo : https://claude.ai/chat/30af4a7e-ecb7-46c2-86c2-b1e84611cda6