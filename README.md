# Omise Documentation Repository

This repository contains the source documentation for Omise. Changes pushed here are automatically synced to the documentation CMS.

## Repository Structure

```
docs/
├── get-started/
│   ├── index.md           # Category index
│   ├── quickstart.md
│   └── installation.md
├── developers/
│   ├── index.md
│   ├── authentication.md
│   ├── webhooks.md
│   └── testing.md
├── merchants/
│   ├── index.md
│   ├── transfers.md
│   └── disputes.md
├── payment-methods/
│   ├── index.md
│   ├── credit-cards.md
│   ├── promptpay.md
│   └── truemoney.md
└── resources/
    ├── index.md
    └── changelog.md
```

## Frontmatter Reference

Each markdown file should include YAML frontmatter at the top:

```yaml
---
title: "Document Title"
slug: "document-slug"
description: "Brief description for SEO and previews"
category: "get-started"
sidebar_label: "Short Label"
position: 1
difficulty: "beginner"
tags: ["api", "getting-started"]
---
```

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Document title displayed as H1 |
| `slug` | string | URL slug (auto-generated from filename if omitted) |

### Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `description` | string | - | SEO meta description |
| `category` | string | - | Category slug for grouping |
| `sidebar_label` | string | title | Short label for sidebar navigation |
| `position` | number | 0 | Order in sidebar (lower = higher) |
| `difficulty` | enum | - | `beginner`, `intermediate`, `advanced` |
| `tags` | array | - | Keywords for search and filtering |
| `reading_time` | number | auto | Reading time in minutes (auto-calculated if omitted) |

## Sync Behavior

### Automatic Sync (Webhook)

1. Push changes to the `main` branch
2. GitHub sends webhook to Strapi
3. Strapi fetches and parses markdown files
4. Documents are created/updated/deleted as needed

### Manual Sync

```bash
# Via Strapi API (requires admin token)
curl -X POST http://localhost:1337/api/github-sync/sync \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"branch": "main"}'
```

### Rollback

To revert to a previous version:

```bash
curl -X POST http://localhost:1337/api/github-sync/rollback \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"commitSha": "abc123..."}'
```

## Example Document

```markdown
---
title: "Getting Started with Omise"
slug: "getting-started"
description: "Learn how to integrate Omise payment gateway in 5 minutes"
category: "get-started"
sidebar_label: "Quick Start"
position: 1
difficulty: "beginner"
tags: ["quickstart", "integration", "api"]
---

# Getting Started with Omise

Welcome to Omise! This guide will help you integrate payments quickly.

## Prerequisites

- An Omise account ([Sign up here](https://dashboard.omise.co/signup))
- API keys from your dashboard

## Installation

Install the Omise SDK for your language:

\`\`\`bash
npm install omise
\`\`\`

## Your First Charge

\`\`\`javascript
const omise = require('omise')({
  secretKey: 'skey_test_...'
});

const charge = await omise.charges.create({
  amount: 100000,
  currency: 'THB',
  card: 'tokn_test_...'
});
\`\`\`

## Next Steps

- [Authentication Guide](/authentication)
- [Accept Credit Cards](/accept-credit-cards)
- [Webhooks](/webhooks)
```

## Categories

Documents are organized into categories. Each category should have an `index.md`:

| Category | Slug | Description |
|----------|------|-------------|
| Get Started | `get-started` | Quick start guides |
| Developers | `developers` | Technical documentation |
| Merchants | `merchants` | Business operations |
| Payment Methods | `payment-methods` | Payment method guides |
| Resources | `resources` | Changelog, FAQ, etc. |

## Internationalization

For localized content, create language subdirectories:

```
docs/
├── en/
│   └── getting-started.md
├── ja/
│   └── getting-started.md
└── th/
    └── getting-started.md
```

Or use frontmatter to specify locale:

```yaml
---
title: "เริ่มต้นใช้งาน"
locale: "th"
---
```

## Contributing

1. Create a feature branch
2. Make your changes
3. Submit a pull request
4. After merge, changes auto-sync to production

## Webhook Configuration

To set up the webhook in GitHub:

1. Go to repository Settings → Webhooks
2. Add webhook:
   - **Payload URL**: `https://your-strapi-url/api/github-sync/webhook`
   - **Content type**: `application/json`
   - **Secret**: Your webhook secret
   - **Events**: Just the push event
3. Save and test

## Environment Variables

Required in Strapi:

```env
GITHUB_REPO_OWNER=your-org
GITHUB_REPO_NAME=omise-docs-content
GITHUB_ACCESS_TOKEN=ghp_...
GITHUB_WEBHOOK_SECRET=your-secret
GITHUB_SYNC_BRANCH=main
GITHUB_DOCS_PATH=docs
```
