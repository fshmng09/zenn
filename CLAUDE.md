# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **Zenn content management repository** using [Zenn CLI](https://zenn.dev/zenn/articles/zenn-cli-guide). Zenn is a Japanese technical writing platform. Content is written locally in Markdown and can be previewed before publishing.

## Key Commands

### Content Creation
```bash
npx zenn new:article        # Create new article with auto-generated slug
npx zenn new:book           # Create new book
```

### Preview & Development
```bash
npx zenn preview            # Start local preview server (http://localhost:8000)
```

### List Content
```bash
npx zenn list:articles      # List all articles
npx zenn list:books         # List all books
```

## Content Structure

### Articles (`articles/`)
- Markdown files with frontmatter format
- Filename pattern: `{slug}.md` (e.g., `202406-cdk-setup.md`)
- Required frontmatter fields:
  - `title`: Article title
  - `emoji`: Display emoji
  - `type`: Either `tech` (technical) or `idea` (idea/opinion)
  - `topics`: Array of topic tags (e.g., `["aws", "cdk"]`)
  - `published`: Boolean (true/false)

### Books (`books/`)
- Currently empty (contains only `.gitkeep`)
- Books are multi-chapter content organized in subdirectories

## Important Notes

1. **Always use `npx zenn` commands** - The CLI is installed as a local dependency, not globally
2. **Preview before publishing** - Use `npx zenn preview` to verify formatting, images, and links
3. **Article naming** - Use descriptive slugs with year-month prefix (e.g., `202406-topic-name.md`)
4. **Content language** - Articles are written in Japanese
5. **No build step** - Markdown files are published directly to Zenn; no compilation needed
