---
tags:
  - ide/antigravity
  - tips-tricks
---

---

- when you install skills globally, (like matt Pocock skills), they default to ~/.agents/skills
- but for some stupid fucking reason antigravity ide doesn't detect them, by default its core daemon is hardcoded to scan specific discovery roots, The installer’s _Universal_ target puts skills in `$HOME\.agents\skills` (plural `.agents`), whereas Antigravity IDE looks in its own global directory—typically **`$HOME\.antigravity\skills`** or **`$HOME\.agent\skills`**
- the best thing to do is a  **Symlink / Junction approach** using this command
```Powershell
New-Item -ItemType Junction -Path "$HOME\.antigravity\skills" -Target "$HOME\.agents\skills"
```
- _(Note: If you already created a folder named `skills` inside `.antigravity`, delete or rename it first so the junction can take its place.)_
- *Note :* You cannot use windows shortcut copy, it doesn't work that way chief , there's no shortcuts here (get it ;) )