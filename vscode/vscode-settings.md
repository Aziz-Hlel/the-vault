---
tags:
  - vscode
  - tips-tricks
  - ksi
---

---


- file name is `extensions.json`
```json
{
// Format Setting
"editor.defaultFormatter": "esbenp.prettier-vscode", // To choose Prettier as formatter
"editor.formatOnSave": true, // To format code on save
"files.eol": "\n",
"editor.codeActionsOnSave": { // Organizer imports and remove unused imports
	"source.organizeImports": "explicit"
},
"[prisma]": {
	"editor.defaultFormatter": "Prisma.prisma" // Choose Prisma as formatter for .prisma files
},

// git settings
"git.autofetch": true,

// tailwind settings
"tailwindCSS.classFunctions": ["tw", "cn"],  // literal names of functions for which to provide class completions, hover previews, linting etc.

// TS Settings
"js/ts.tsdk.path": "node_modules\\typescript\\lib", // To use the same TypeScript version as in the project
"typescript.tsdk": "node_modules\\typescript\\lib", // To use the same TypeScript version as in the project

// Enable Ts-go as engine
"typescript.experimental.useTsgo": false,  // Enable ts-go (optional)
"js/ts.experimental.useTsgo": false,  // Enable ts-go (optional)
}
```

