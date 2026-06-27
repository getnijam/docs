# Nijam Docs, Claude instructions

Astro + Starlight documentation site for Nijam (content under `src/content/docs/**`, MD/MDX). Deployed via Vercel.

## Guard rails, do NOT

- ❌ **Em dashes (U+2014) or en dashes (U+2013) anywhere, never generate one.** Not in page prose, frontmatter `title`/`description`, taglines, card copy, or anywhere else. Use a comma, colon, parentheses, or split into two sentences. A plain hyphen-minus (`-`) is only for compound words, ranges, and CLI flags. When editing prose, prefer a comma or colon over a spaced hyphen. Never alter code samples inside fenced code blocks to satisfy this rule.
