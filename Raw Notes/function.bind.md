---
tags:
  - languages/typescript
  - ksi
---

---


- `bind` creates a copy of a function with its `this` permanently set to the object you choose. It doesn't call the function, it just gives you a new one to call later.

- In JavaScript, `this` is decided by how a function is called, not where it was defined:****

```ts
const user = {
  name: 'Sam',
  hello() { return this.name; },
};

user.hello();              // 'Sam', because it's called on user, so this = user

const fn = user.hello;
fn();                      // breaks, because it's called on nothing, so this is undefined

const bound = user.hello.bind(user);
bound();                   // 'Sam', because this is locked to user

```


- A case where you 'were' going to use it :
```ts
/** Express doesn't keep the response body, so store it for the `res` serializer above. */
export const captureResponseBody: RequestHandler = (_, res, next) => {
  if (isProd()) return next();
  const sendJson = res.json.bind(res);
  res.json = (body) => {
    res.locals.responseBody = body;
    return sendJson(body);
  };
  next();
};
```

- That way you can then use `res.locals.responseBody` to log the response body later on 