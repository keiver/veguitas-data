# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **data-only repository** that serves as the database for [veguitas.com](https://veguitas.com). The main application (in the `veguitas.com` repo) reads and writes to this repository via the GitHub Contents API, treating GitHub as a serverless JSON database.

## Architecture

```
veguitas-data (this repo)          veguitas.com (consumer app)
├── drafts/                        └── src/lib/github-data.ts
│   ├── index.json                     ├── fetchPublicFile() → public reads
│   └── {id}.json                      ├── fetchFile() → authenticated reads
├── posts/                             ├── saveFile() → commits via API
│   ├── index.json                     ├── deleteFile() → deletes via API
│   └── {slug}.json                    └── listDirectory()
├── versions/
│   └── {draft-id}/
│       └── v{n}.json
├── categories.json
├── settings.json
└── schemas/
```

## Data Flow

1. **Draft Creation** → New entry in `drafts/index.json` + individual file `drafts/{id}.json`
2. **Content Generation** → Claude generates HTML, saved to `versions/{draftId}/v{n}.json`
3. **Publishing** → Draft promoted to `posts/{slug}.json` + entry in `posts/index.json`

## Key Files

| File | Purpose |
|------|---------|
| `categories.json` | Amazon product categories with slugs and URLs |
| `settings.json` | Site config (affiliate tag, analytics, social links) |
| `drafts/index.json` | Array of draft metadata |
| `posts/index.json` | Array of published post metadata |

## Schema Validation

All data structures are defined in `schemas/` using JSON Schema draft 2020-12:

- **draft.schema.json** - Draft status: `draft` or `published`, tracks `currentVersion`
- **post.schema.json** - Published content with `html`, `products[]`, SEO metadata
- **product.schema.json** - Amazon product (ASIN, price, rating, affiliate URL)
- **version.schema.json** - Generated content snapshot with model/prompt metadata

## ID Conventions

- **Draft IDs**: `draft_{timestamp}_{random}` (e.g., `draft_1767990418594_1ojopxf`)
- **Post slugs**: Lowercase, hyphenated (pattern: `^[a-z0-9-]+$`)
- **Version files**: `v{n}.json` where n starts at 1

## Important Constraints

- All affiliate links must use tag `veguitas-20`
- Product ASINs are exactly 10 characters
- Slugs are immutable after publishing
- Version numbers only increment, never reset
- HTML content includes embedded CSS animations (no external stylesheets)

## No Build System

This is a pure data repository. There are no:
- Package managers (npm, yarn)
- Build scripts
- Test suites
- Linting configurations

All validation happens in the consumer application via Zod schemas.
