---
tags:
  - fun-fact
  - lessons-learned
---
- An `<img>` tag (like `<script>`, `<link>`, `<video>` and CSS `background-image`) makes a plain GET request that the browser builds itself, and HTML gives you no way to attach custom headers such as `Authorization`. The only thing you can influence is whether credentials are sent, via the `crossorigin` attribute `use-credentials` which controls cookies, not arbitrary headers.