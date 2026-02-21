# English Article SEO Improvement Design

## Problem

English articles receive almost no traffic despite being published alongside Japanese versions. The blog has bilingual content (48 articles) but organic search traffic comes almost entirely from Japanese articles.

## Root Causes

1. **Missing `description` meta tags**: 46 of 48 English articles lack `description` in frontmatter, resulting in empty meta descriptions and poor search visibility
2. **Draft English articles**: 4 articles are `draft = true` in English while published in Japanese
3. **Japanese-only sidebar subtitle**: English pages display a Japanese subtitle, degrading user experience for English visitors

## Design

### 1. Add `description` to all English articles

- Target: 46 `index.en.md` files missing `description`
- Create natural English descriptions (120-160 chars) based on the corresponding Japanese `description`
- Include primary keywords (tool names, technologies)
- Focus on reader benefit (e.g., "Learn how to...", "Step-by-step guide for...")

### 2. Publish draft English articles

Set `draft = false` for:
- `010-favicon/index.en.md`
- `035-github-copilot-markdown/index.en.md`
- `037-gemini-cli/index.en.md`
- `042-github-copilot/index.en.md`

Also add `description` to these articles.

### 3. Localize sidebar subtitle

Add English sidebar subtitle via Hugo language-specific params:

```toml
# In config/_default/config.toml under [languages.en]
[languages.en.params.sidebar]
subtitle = "A blog about engineering and management by an engineering manager"
```

## Success Criteria

- All 48 English articles have `description` in frontmatter
- No English articles remain in draft status (unless intentionally unpublished)
- English pages show English sidebar subtitle
- `hugo --minify` builds without errors
