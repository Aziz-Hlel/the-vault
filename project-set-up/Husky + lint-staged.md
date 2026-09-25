
---

- husky runs command pre and post git commands such as commits and pushes 
``` bash
pnpm i -D husky
```


- add to `package.json` : 
``` json
  "scripts": {
    "prepare": "husky || true",
}
```
-  you add the OR true to  make it so that it running `pnpm i` in prod wouldn't crash since husky is a dev dependency only

- create `.husky/pre-commit` and write : 
```bash
lint-staged
```

- next up is to install `lint-stage`, lint-stage lets you run tasks like formatters and linters against staged git files
```bash
pnpm i -D lint-staged
```

-  now you can add and set up [[Prettier]] and eslint to run on staged files pre commiting by adding these line in the `package.json` : 
```json
  "lint-staged": {
    "**/*.{js,mjs,cjs,jsx,ts,mts,cts,tsx}": "prettier --write --ignore-unknown"
  },
```