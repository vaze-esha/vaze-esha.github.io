---
name: feedback-footnotes
description: Always hyperlink footnotes — superscript refs link down to the footnote, footnotes have back-links up to the ref
metadata:
  type: feedback
---

Always hyperlink footnotes in HTML pages. Superscript refs in body text should be anchor links pointing to the footnote (`<a href="#fnN" id="fnrefN">`), and each footnote item should have a matching `id="fnN"` and a back-link (`<a href="#fnrefN">↩</a>`) after the superscript.

**Why:** User preference stated explicitly — reduces friction when reading footnote-heavy posts.

**How to apply:** Any time footnotes appear in a blog post or page, wire up both directions. Also add `.footnote-ref a { color: black; text-decoration: none; }` to style.css so the link color matches the existing footnote-ref style.
