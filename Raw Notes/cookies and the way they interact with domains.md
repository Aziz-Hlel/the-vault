---
tags:
  - lessons-learned
  - fun-fact
  - ksi
---

---

- question : can i set cookies from my server , to be send with the frontend each time they retrieve a media from my cloudfront cdn ? 
- Cookies belong to the domain that set them, so a cookie set by `example.com` is never sent to `cloudfront.net`. There’s also no way for your server to set a cookie for `cloudfront.net`.