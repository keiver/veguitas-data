# Veguitas Data

Content repository for [veguitas.com](https://veguitas.com) — stored as JSON files and accessed via GitHub's Contents API as a serverless CMS.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Repository Structure

```
veguitas-data/
├── categories.json       # Product categories 
├── settings.json         # Site configuration
│
├── drafts/
│   ├── index.json        # Draft metadata index
│   └── {id}.json         # Individual draft content
│
├── posts/
│   ├── index.json        # Published posts index
│   └── {slug}.json       # Full post with HTML + products
│
├── versions/
│   └── {draft-id}/
│       └── v{n}.json     # Content generation snapshots
│
└── schemas/              # JSON Schema 2020-12 definitions
    ├── draft.schema.json
    ├── post.schema.json
    ├── product.schema.json
    ├── version.schema.json
    ├── category.schema.json
    └── settings.schema.json
```

## Data Schemas

All data is validated against JSON Schema draft 2020-12. Schemas live in `/schemas/`.

### Draft

Content in progress, not yet published.

```json
{
  "id": "draft_1767992999533_mljposn",
  "title": "Best solar panels for Cuba",
  "slug": "best-solar-panels-for-cuba",
  "status": "draft",
  "categorySlug": "solar-panels",
  "listType": "best-sellers",
  "productCount": 5,
  "currentVersion": 2,
  "createdAt": "2026-01-09T21:09:59.533Z",
  "updatedAt": "2026-01-09T22:27:36.488Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Format: `draft_{timestamp}_{random}` |
| `status` | enum | `"draft"` or `"published"` |
| `listType` | enum | `"best-sellers"`, `"movers-and-shakers"`, `"new-releases"`, `"most-wished-for"` |
| `currentVersion` | integer | Latest version number (0 = no content generated) |

### Post

Published content with full HTML and product data.

```json
{
  "slug": "best-solar-panels-for-cuba",
  "title": "Best solar panels for Cuba",
  "excerpt": "Top 5 Solar Panels products reviewed",
  "html": "<article>...</article>",
  "categorySlug": "solar-panels",
  "products": [...],
  "featuredImage": "https://m.media-amazon.com/...",
  "metaTitle": "5 Best Solar Panels for Cuba (2026)",
  "metaDescription": "Discover the top solar panels...",
  "publishedAt": "2026-01-09T21:34:01.237Z",
  "updatedAt": "2026-01-09T22:37:07.426Z"
}
```

### Product

Amazon product data with affiliate tracking.

```json
{
  "asin": "B0BSHRYXLZ",
  "title": "100W Portable Solar Panel",
  "price": 89.99,
  "rating": 4.5,
  "reviewCount": 1247,
  "imageUrl": "https://m.media-amazon.com/images/I/...",
  "affiliateUrl": "https://amazon.com/dp/B0BSHRYXLZ?tag=veguitas-20",
  "rank": 1,
  "badge": "Amazon's Choice",
  "pros": ["Lightweight design", "High efficiency"],
  "cons": ["No mounting hardware included"]
}
```

### Version

Snapshot of AI-generated content for a draft.

```json
{
  "version": 3,
  "html": "<article>...</article>",
  "products": [...],
  "prompt": "Write an engaging product review...",
  "model": "claude-opus-4-5-20251101",
  "createdAt": "2026-01-09T22:15:00.000Z"
}
```

## Index Files

Index files (`drafts/index.json`, `posts/index.json`) contain metadata arrays for listing views. Full content lives in individual files to minimize payload sizes.

```json
// posts/index.json
[
  {
    "slug": "best-solar-panels-for-cuba",
    "title": "Best solar panels for Cuba",
    "excerpt": "Top 5 Solar Panels products reviewed",
    "categorySlug": "solar-panels",
    "productCount": 5,
    "publishedAt": "2026-01-09T21:34:01.237Z"
  }
]
```

## GitHub as a Serverless CMS for low-frequency content generation at minimal cost, free hosting, and built-in version history.

This repository is consumed via GitHub's Contents API, treating Git as a document database.

### Tradeoffs

| Advantage | Limitation |
|-----------|------------|
| Zero infrastructure cost | 5,000 API requests/hour rate limit |
| Built-in version history | No complex queries (must denormalize) |
| Free hosting forever | ~100MB repo size limit practical |
| Git-native collaboration | Write latency (~500ms per commit) |
| Works offline (clone locally) | Public repo = public data |
| No database migrations | No transactions or atomic operations |
| Automatic backups via Git | Not suitable for user-generated content |

### When This Pattern Works

- Content sites with low write frequency
- Projects prioritizing cost over performance
- Static or semi-static data (categories, settings, posts)
- Small teams where Git workflow is natural

### When to Avoid

- High-frequency writes (>100/hour)
- User-generated content at scale
- Complex relational queries needed
- Real-time collaboration requirements

## Related

- [veguitas.com](https://github.com/keiver/veguitas.com) — Next.js application that consumes this data

## License

MIT License - See [LICENSE](LICENSE)
