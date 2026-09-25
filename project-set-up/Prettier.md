
---



```bash
pnpm i -D prettier
pnpm i -D -w prettier-plugin-tailwindcss 
```

- prettier-plugin-tailwindcss is to order tailwind attributes

- add a `.prettierignore`

```text
pnpm i -D prettier
pnpm i -D -w prettier-plugin-tailwindcss 
```

- add `.prettierrc.json` : 

```json
{
  "semi": true,
  "singleQuote": true,
  "jsxSingleQuote": true,
  "trailingComma": "all",
  "endOfLine": "lf",
  "tabWidth": 2,
  "plugins": ["prettier-plugin-tailwindcss"],
  "printWidth": 120
}
```


## Add Prettier to lint-stage

- prerequisite: [[Husky + lint-staged]]
- in `package.json` add : 

```json
 "lint-staged": {
    "**/*.{js,mjs,cjs,jsx,ts,mts,cts,tsx}": "prettier --write --ignore-unknown"
  }
```

