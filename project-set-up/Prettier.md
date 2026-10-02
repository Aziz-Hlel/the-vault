
---



```bash
pnpm i -D prettier
pnpm i -D -w prettier-plugin-tailwindcss 
```

- prettier-plugin-tailwindcss is to order tailwind attributes

- add a `.prettierignore`

```bash
pnpm i -D prettier
pnpm i -D -w prettier-plugin-tailwindcss 
#plugins
pnpm i -D -w @trivago/prettier-plugin-sort-imports prettier-plugin-tailwindcss
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
  "plugins": [
    "@trivago/prettier-plugin-sort-imports", // To sort import declarations
    "prettier-plugin-tailwindcss", // To sort your Tailwind CSS utility classes
  ],
  "tailwindStylesheet": "./src/styles/index.css", // So it can sort custom utilities
  "printWidth": 120
}
```


## Add Prettier to lint-stage

- prerequisite: [[Husky + lint-staged]]
- in `package.json` add : 

```json
 "lint-staged": {
    "**/*.{js,mjs,cjs,jsx,ts,mts,cts,tsx,json,md}": "prettier --write --ignore-unknown"
  }
```

