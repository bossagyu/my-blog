# English SEO Improvement Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add SEO descriptions to all English articles, publish draft articles, and localize sidebar to improve English search visibility.

**Architecture:** Edit frontmatter in 44 `index.en.md` files to add `description`, set `draft = false` on 4 articles, and add English sidebar params in `config/_default/config.toml`.

**Tech Stack:** Hugo static site generator, TOML frontmatter

---

### Task 1: Add English sidebar subtitle

**Files:**
- Modify: `config/_default/config.toml:22-26`

**Step 1: Add English sidebar params**

Change:
```toml
  [languages.en]
    title = "Bossagyu Blog"
    languageName = "en-US 🇺🇸"
    LanguageCode = "en-US"
    [languages.en.params]
```

To:
```toml
  [languages.en]
    title = "Bossagyu Blog"
    languageName = "en-US 🇺🇸"
    LanguageCode = "en-US"
    [languages.en.params]
    [languages.en.params.sidebar]
      subtitle = "A blog about engineering and management by an engineering manager"
```

**Step 2: Verify build**

Run: `hugo --minify`
Expected: Builds without errors

**Step 3: Verify sidebar in English page**

Run: `hugo server` and check http://localhost:1313/en/
Expected: English subtitle displayed in sidebar

**Step 4: Commit**

```bash
git add config/_default/config.toml
git commit -m "feat: add English sidebar subtitle for multilingual SEO"
```

---

### Task 2: Publish draft English articles and add descriptions

**Files:**
- Modify: `content/blog/010-favicon/index.en.md`
- Modify: `content/blog/035-github-copilot-markdown/index.en.md`
- Modify: `content/blog/037-gemini-cli/index.en.md`
- Modify: `content/blog/042-github-copilot/index.en.md`

**Step 1: Update 010-favicon/index.en.md**

Set `draft = false` and add description:
```toml
description = 'How to create a favicon and set it up on a Hugo blog. Covers image generation with Favicon Generator and configuration in config.toml.'
draft = false
```

**Step 2: Update 035-github-copilot-markdown/index.en.md**

Set `draft = false` and add description:
```toml
description = 'How to enable GitHub Copilot suggestions for Markdown files in VSCode. A quick settings change to boost your Markdown editing productivity.'
draft = false
```

**Step 3: Update 037-gemini-cli/index.en.md**

Set `draft = false` and add description:
```toml
description = 'How to install Gemini CLI on Mac with Homebrew and use Google Gemini AI from the terminal. Covers setup, Google account linking, and code generation.'
draft = false
```

**Step 4: Update 042-github-copilot/index.en.md**

Set `draft = false` and add description:
```toml
description = 'How to use GitHub Copilot Chat tools in VSCode for better productivity. Covers codebase search, selection, terminal commands, and web page fetching.'
draft = false
```

**Step 5: Verify build**

Run: `hugo --minify`
Expected: Builds without errors, 4 additional English pages generated

**Step 6: Commit**

```bash
git add content/blog/010-favicon/index.en.md content/blog/035-github-copilot-markdown/index.en.md content/blog/037-gemini-cli/index.en.md content/blog/042-github-copilot/index.en.md
git commit -m "feat: publish draft English articles with SEO descriptions"
```

---

### Task 3: Add descriptions to English articles 001-015

**Files:**
- Modify: `content/blog/001-hugo-netlify-build/index.en.md`
- Modify: `content/blog/002-line-messaging-api/index.en.md`
- Modify: `content/blog/003-google-analytics/index.en.md`
- Modify: `content/blog/004-python-setup/index.en.md`
- Modify: `content/blog/005-github-copilot/index.en.md`
- Modify: `content/blog/006-intellij-lamda-setup/index.en.md`
- Modify: `content/blog/007-google-search-console/index.en.md`
- Modify: `content/blog/008-aws-eventbrdge/index.en.md`
- Modify: `content/blog/009-light-house/index.en.md`
- Modify: `content/blog/011-hugo-multilingul-support/index.en.md`
- Modify: `content/blog/012-social-card/index.en.md`
- Modify: `content/blog/013-good-strategy-bad-strategy/index.en.md`
- Modify: `content/blog/014-aws-apigateway-lambda/index.en.md`
- Modify: `content/blog/015-s3-object-check/index.en.md`

**Descriptions to add (after `title` line in frontmatter):**

| File | Description |
|------|-------------|
| 001 | `description = 'How to publish a blog using Hugo, Netlify, and GitHub. A beginner-friendly guide covering static site generation, GitHub integration, Netlify deployment, and favicon setup.'` |
| 002 | `description = 'How to register for LINE Messaging API and send messages with curl. Step-by-step guide from provider creation to channel setup and Bot messaging.'` |
| 003 | `description = 'How to set up Google Analytics GA4 on a Hugo blog. Covers tracking ID setup, config.toml configuration, and embedding analytics tags in templates.'` |
| 004 | `description = 'How to set up a Python development environment on Mac using Pyenv and venv. Covers version management, virtual environment creation, updates, and deletion.'` |
| 005 | `description = 'How to use GitHub Copilot in IntelliJ. Covers plugin installation, Mac keyboard shortcuts, and code completion with comment-driven suggestions.'` |
| 006 | `description = 'How to develop Lambda functions with AWS Toolkit in IntelliJ. Covers Docker and AWS CLI setup, Lambda creation, and local execution steps.'` |
| 007 | `description = 'How to use Google Search Console to make your blog discoverable on Google. Covers domain verification, sitemap registration, and index request steps.'` |
| 008 | `description = 'How to schedule Lambda functions with AWS EventBridge. Covers cron expressions, JST timezone settings, costs, and common troubleshooting tips.'` |
| 009 | `description = 'How to use Google Lighthouse to measure blog performance. Covers Performance, Accessibility, SEO, and Best Practices scores and how to interpret them.'` |
| 011 | `description = 'How to make a Hugo blog multilingual using ChatGPT. Covers Markdown translation prompts, config.toml settings, and directory structure for i18n support.'` |
| 012 | `description = 'How to set up Twitter Social Cards for a Hugo blog. Covers Summary Card vs Large Image types, theme-specific settings, and debugging tools.'` |
| 013 | `description = 'How to create an effective product strategy based on "Good Strategy Bad Strategy." Covers the three core elements: diagnosis, guiding policy, and coherent actions.'` |
| 014 | `description = 'How to integrate AWS API Gateway with Lambda. Covers REST API vs HTTP API selection, proxy integration setup, and trigger configuration steps.'` |
| 015 | `description = 'How to check if an S3 object exists using Python boto3. Covers the recommended head_object method, resource vs client differences, and common pitfalls.'` |

**Step 1: Add descriptions to each file**

For each file, add the `description` line after the `title` line in the TOML frontmatter block (`+++`).

**Step 2: Verify build**

Run: `hugo --minify`
Expected: Builds without errors

**Step 3: Commit**

```bash
git add content/blog/001-hugo-netlify-build/index.en.md content/blog/002-line-messaging-api/index.en.md content/blog/003-google-analytics/index.en.md content/blog/004-python-setup/index.en.md content/blog/005-github-copilot/index.en.md content/blog/006-intellij-lamda-setup/index.en.md content/blog/007-google-search-console/index.en.md content/blog/008-aws-eventbrdge/index.en.md content/blog/009-light-house/index.en.md content/blog/011-hugo-multilingul-support/index.en.md content/blog/012-social-card/index.en.md content/blog/013-good-strategy-bad-strategy/index.en.md content/blog/014-aws-apigateway-lambda/index.en.md content/blog/015-s3-object-check/index.en.md
git commit -m "feat: add SEO descriptions to English articles 001-015"
```

---

### Task 4: Add descriptions to English articles 016-029

**Files:**
- Modify: `content/blog/016-itilv4-availability-management/index.en.md`
- Modify: `content/blog/017-vscode-copilot/index.en.md`
- Modify: `content/blog/018-itilv4-business-analysis/index.en.md`
- Modify: `content/blog/019-stable-diffusion/index.en.md`
- Modify: `content/blog/020-itilv4-capacity-and-performance-management/index.en.md`
- Modify: `content/blog/021-typescript-setup/index.en.md`
- Modify: `content/blog/022-typescript-enum/index.en.md`
- Modify: `content/blog/023-chatgpt-create-image/index.en.md`
- Modify: `content/blog/024-bluesky-api/index.en.md`
- Modify: `content/blog/025-git-ignore/index.en.md`
- Modify: `content/blog/026-evernote-to-notion/index.en.md`
- Modify: `content/blog/027-chatgpt-4o/index.en.md`
- Modify: `content/blog/028-scrum-ceremony/index.en.md`
- Modify: `content/blog/029-grpc/index.en.md`

**Descriptions to add:**

| File | Description |
|------|-------------|
| 016 | `description = 'Understanding ITIL v4 availability management. Covers service availability control, analysis, improvement processes, SLA design, and dashboard examples from real experience.'` |
| 017 | `description = 'How to set up and use GitHub Copilot in VSCode. Covers extension installation, Markdown support configuration, and Chat tool usage for maximum productivity.'` |
| 018 | `description = 'Understanding ITIL v4 business analysis. Covers SWOT analysis, customer journey mapping, and solution identification processes with practical insights.'` |
| 019 | `description = 'How to install Stable Diffusion Web UI on Mac for local image generation. Covers Python environment setup, model placement, and image generation steps.'` |
| 020 | `description = 'Understanding ITIL v4 capacity and performance management. Covers latency requirements, throughput measurement, and Dynatrace monitoring with practical examples.'` |
| 021 | `description = 'How to set up a TypeScript development environment on Mac with Volta. Covers Node.js installation, yarn setup, project creation, and Hello World execution.'` |
| 022 | `description = 'How to use Enums in TypeScript. Covers numeric and string enum definitions, string comparison patterns, and JavaScript compilation output examples.'` |
| 023 | `description = 'How to generate images with ChatGPT Plus using DALL-E. Covers launching from Explore GPTs, prompt input examples, and refining images with follow-up instructions.'` |
| 024 | `description = 'How to automate Bluesky posts using Python and AT Protocol. Covers app password generation, posting script creation, and execution steps.'` |
| 025 | `description = 'How to set up a global gitignore at ~/.config/git/ignore for all projects. Includes config examples for .idea, node_modules, and other common exclusions.'` |
| 026 | `description = 'How to migrate from Evernote to Notion easily. Covers the Notion import feature and tips for migrating notebooks one at a time.'` |
| 027 | `description = 'Overview of OpenAI GPT-4o features. Covers real-time voice interaction, faster responses, improved multilingual support, and key upgrades from previous models.'` |
| 028 | `description = 'How to improve team performance through Scrum ceremonies. Covers optimization tips for daily standups, sprint reviews, and backlog refinement.'` |
| 029 | `description = 'Introduction to gRPC with Python implementation. Covers Protocol Buffers schema definition, .proto file syntax, and a hands-on Quick Start tutorial.'` |

**Step 1: Add descriptions to each file**

**Step 2: Verify build**

Run: `hugo --minify`
Expected: Builds without errors

**Step 3: Commit**

```bash
git add content/blog/016-itilv4-availability-management/index.en.md content/blog/017-vscode-copilot/index.en.md content/blog/018-itilv4-business-analysis/index.en.md content/blog/019-stable-diffusion/index.en.md content/blog/020-itilv4-capacity-and-performance-management/index.en.md content/blog/021-typescript-setup/index.en.md content/blog/022-typescript-enum/index.en.md content/blog/023-chatgpt-create-image/index.en.md content/blog/024-bluesky-api/index.en.md content/blog/025-git-ignore/index.en.md content/blog/026-evernote-to-notion/index.en.md content/blog/027-chatgpt-4o/index.en.md content/blog/028-scrum-ceremony/index.en.md content/blog/029-grpc/index.en.md
git commit -m "feat: add SEO descriptions to English articles 016-029"
```

---

### Task 5: Add descriptions to English articles 030-048

**Files:**
- Modify: `content/blog/030-go-environment-construction/index.en.md`
- Modify: `content/blog/031-read-parquet-file/index.en.md`
- Modify: `content/blog/032-python-uv/index.en.md`
- Modify: `content/blog/033-feast-tutorial/index.en.md`
- Modify: `content/blog/034-jetbrains-update/index.en.md`
- Modify: `content/blog/036-vscode-indent/index.en.md`
- Modify: `content/blog/039-aws-toolkit-vscode/index.en.md`
- Modify: `content/blog/040-redis-local/index.en.md`
- Modify: `content/blog/041-redis-go/index.en.md`
- Modify: `content/blog/043-codex/index.en.md`
- Modify: `content/blog/044-openai-response-api/index.en.md`
- Modify: `content/blog/046-clean-bot-technical/index.en.md`

Note: 038, 048 already have descriptions. 030-covariance, 045, 047 have no English version.

**Descriptions to add:**

| File | Description |
|------|-------------|
| 030 | `description = 'How to set up a Go development environment on Mac. Covers Homebrew installation, Hello World program creation, execution, and binary build steps.'` |
| 031 | `description = 'How to read Parquet files on macOS from the command line. Covers parquet-cli installation and usage of meta, head, and schema commands.'` |
| 032 | `description = 'How to set up a Python environment on Mac with uv, a fast Rust-based package manager. Covers installation, virtual environment creation, and package management.'` |
| 033 | `description = 'How to set up Feast Feature Store on macOS. Covers installation, UI launch, training data retrieval, and materialization to the online store.'` |
| 034 | `description = 'How to upgrade JetBrains products like IntelliJ and PyCharm to a major version. Use JetBrains Toolbox App to update while preserving settings.'` |
| 036 | `description = 'How to customize VSCode indent guide colors in settings. Change indent colors by depth level without extensions to improve code readability.'` |
| 039 | `description = 'How to install AWS Toolkit in VSCode and configure Tokyo region (ap-northeast-1). Covers AWS Explorer region switching steps.'` |
| 040 | `description = 'How to run Redis with Docker. Covers container setup for the in-memory database, basic operations, connection testing, and container cleanup.'` |
| 041 | `description = 'How to use Redis with Go. Covers go-redis library setup, connection, data read/write operations, and basic commands with sample code.'` |
| 043 | `description = 'How to install and use Codex CLI. Covers npm and Homebrew installation, sign-in process, basic commands, and local development workflow.'` |
| 044 | `description = 'How to use the OpenAI Response API. Covers API key setup, Python text generation, and tool calling with minimal code examples to get started.'` |
| 046 | `description = 'Building a cleaning reminder LINE Bot with AWS Lambda, API Gateway, S3, and EventBridge. Covers SAM serverless architecture and Python implementation details.'` |

**Step 1: Add descriptions to each file**

**Step 2: Verify build**

Run: `hugo --minify`
Expected: Builds without errors

**Step 3: Commit**

```bash
git add content/blog/030-go-environment-construction/index.en.md content/blog/031-read-parquet-file/index.en.md content/blog/032-python-uv/index.en.md content/blog/033-feast-tutorial/index.en.md content/blog/034-jetbrains-update/index.en.md content/blog/036-vscode-indent/index.en.md content/blog/039-aws-toolkit-vscode/index.en.md content/blog/040-redis-local/index.en.md content/blog/041-redis-go/index.en.md content/blog/043-codex/index.en.md content/blog/044-openai-response-api/index.en.md content/blog/046-clean-bot-technical/index.en.md
git commit -m "feat: add SEO descriptions to English articles 030-048"
```

---

### Task 6: Final verification

**Step 1: Full build check**

Run: `hugo --minify`
Expected: Builds without errors

**Step 2: Verify English meta descriptions in HTML output**

Run: `grep -r 'meta.*description' public/en/blog/001-hugo-netlify-build/index.html | head -3`
Expected: Meta description tag with the English description content

**Step 3: Verify sidebar subtitle**

Run: `grep -r "engineering manager" public/en/index.html`
Expected: English subtitle found in sidebar

**Step 4: Verify no remaining drafts**

Run: `grep -rl 'draft = true' content/blog/*/index.en.md`
Expected: No results (all English articles published)

**Step 5: Verify description coverage**

Run: `for f in content/blog/*/index.en.md; do grep -qL 'description' "$f" && echo "MISSING: $f"; done`
Expected: No missing descriptions
