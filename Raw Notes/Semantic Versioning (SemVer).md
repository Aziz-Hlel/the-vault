---
tags:
  - concept
  - "#ksi"
---

---

- Many libraries follow **Semantic Versioning (SemVer)**:
   **MAJOR.MINOR.PATCH**

- For example, `7.1.3`:
	- **7** = major version
	- **1** = minor version
	- **3** = patch version


- A practical approach:
	- **Major update (`6 → 7`)**: assume breaking changes; read the migration guide.
	- **Minor update (`7.1 → 7.8`)**: usually safe, but skim the release notes.
	- **Patch update (`7.8.1 → 7.8.2`)**: generally the safest.

- However **Not every project follows SemVer strictly**

- For example, in pnpm packages, you'll often see version ranges:
	- `^7.1.0` → allows updates up to but not including `8.0.0`.
	- `~7.1.0` → allows updates up to but not including `7.2.0`.