---
tags:
  - donts
  - war-stories
  - concept
  - ksi
---

---



- I know that on surface level that should be a giving, specially saying long/complicated already seems something to be avoided anyway, but let me go in depth and show you example to really grasp this rule, because sometimes adding another field or two to the form seems harmless and less effort than making a ui for them , their own form and their own api, then adding one more since both of those already in that form then you find yourself clusterfucking your api and server.
- Mehyar's kindergarden management project, includes l'ile aux enfants, fairytopia etc , he has an edit student form where you can edit students general info, his status, parents associated, change his classroom etc, 
- You might think that's not so bad, but let me show you why this form is already fucked as it is, because you need to handle updating the parents, removing old one , verifying new ones exists, if not you need to handle that and decide if you'd cancel the whole operation and return and failed or send a partial fail which means the client need to handle and display and warn the client  