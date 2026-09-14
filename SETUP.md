# GitHub → Strapi Sync Setup Guide

This document explains how to set up the GitHub-to-Strapi documentation sync system.

## Architecture Overview

```
┌─────────────────┐     webhook      ┌─────────────────┐      API       ┌─────────────────┐
│   GitHub Repo   │ ───────────────► │   Strapi CMS    │ ─────────────► │   Next.js App   │
│  (docs/*.md)    │                  │  (sync service) │                │   (frontend)    │
└─────────────────┘                  └─────────────────┘                └─────────────────┘
        │                                    │
        │ git push/revert                    │ parse markdown
        ▼                                    │ upsert documents
   version control                           ▼
   (full history)                       PostgreSQL/SQLite
```

## Step 1: Create GitHub Repository

1. Create a new repository (e.g., `omise-docs-content`)
2. Copy contents of `docs-repo-template/` to the new repo
3. Push to GitHub

```bash
# Clone template
cp -r docs-repo-template/* your-new-repo/
cd your-new-repo

# Initialize git
git init
git add .
git commit -m "Initial documentation structure"
git remote add origin git@github.com:YOUR_ORG/omise-docs-content.git
git push -u origin main
```

## Step 2: Configure Strapi Environment

Add these environment variables to your Strapi installation:

```env
# GitHub Sync Configuration
GITHUB_REPO_OWNER=your-github-org
GITHUB_REPO_NAME=omise-docs-content
GITHUB_ACCESS_TOKEN=ghp_xxxxxxxxxxxx
GITHUB_WEBHOOK_SECRET=your-random-secret-string
GITHUB_SYNC_BRANCH=main
GITHUB_DOCS_PATH=docs
```

### Generate GitHub Access Token

1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Generate new token (classic) with `repo` scope
3. Copy token to `GITHUB_ACCESS_TOKEN`

### Generate Webhook Secret

```bash
# Generate a secure random secret
openssl rand -hex 32
```

## Step 3: Configure GitHub Webhook

1. Go to your docs repository on GitHub
2. Settings → Webhooks → Add webhook
3. Configure:

| Field | Value |
|-------|-------|
| Payload URL | `https://your-strapi-url/api/github-sync/webhook` |
| Content type | `application/json` |
| Secret | Same value as `GITHUB_WEBHOOK_SECRET` |
| Events | Just the `push` event |
| Active | ✓ |

4. Save and test the webhook

## Step 4: Deploy Strapi

After configuring environment variables, restart Strapi:

```bash
cd apps/cms
npm run build
pm2 restart strapi-omise
```

## Step 5: Initial Sync

Trigger the first sync manually:

```bash
# Get an admin API token from Strapi admin panel
# Settings → API Tokens → Create new token

curl -X POST https://your-strapi-url/api/github-sync/sync \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"branch": "main"}'
```

Or use the preview endpoint first to see what will be synced:

```bash
curl -X POST https://your-strapi-url/api/github-sync/preview \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"branch": "main"}'
```

## API Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/github-sync/webhook` | GitHub webhook receiver | Signature |
| POST | `/api/github-sync/sync` | Manual sync trigger | Admin |
| POST | `/api/github-sync/rollback` | Rollback to commit | Admin |
| POST | `/api/github-sync/preview` | Preview changes | Admin |
| GET | `/api/github-sync/status` | Sync status & config | Admin |
| GET | `/api/github-sync/history` | Sync history | Admin |

## Workflow

### Adding/Editing Documentation

1. Edit markdown files in the `docs/` directory
2. Commit and push to `main` branch
3. Webhook triggers automatically
4. Strapi syncs changes within seconds
5. Next.js displays updated content

### Rolling Back

If you need to revert to a previous version:

```bash
# Option 1: Git revert (creates new commit)
git revert HEAD
git push

# Option 2: Direct rollback via API
curl -X POST https://your-strapi-url/api/github-sync/rollback \
  -H "Authorization: Bearer YOUR_API_TOKEN" \
  -d '{"commitSha": "abc123..."}'
```

### Checking Sync Status

```bash
curl https://your-strapi-url/api/github-sync/status \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

## Troubleshooting

### Webhook Not Triggering

1. Check GitHub webhook delivery history
2. Verify webhook secret matches
3. Check Strapi logs: `pm2 logs strapi-omise`

### Sync Failing

1. Check sync history: `GET /api/github-sync/history`
2. Verify GitHub token has `repo` access
3. Check file paths start with `docs/`

### Documents Not Appearing

1. Verify frontmatter has required `title` field
2. Check category slug matches existing category
3. Ensure file ends with `.md`

## Security Notes

- Never commit API tokens to the repository
- Use environment variables for all secrets
- Webhook signature verification is enabled by default
- Admin endpoints require authentication

## File Structure

```
docs/
├── get-started/
│   ├── getting-started.md
│   └── quickstart.md
├── developers/
│   ├── authentication.md
│   ├── webhooks.md
│   └── testing.md
├── merchants/
│   ├── transfers.md
│   └── disputes.md
├── payment-methods/
│   ├── credit-cards.md
│   ├── promptpay.md
│   └── truemoney.md
└── resources/
    └── changelog.md
```

Each file must have YAML frontmatter:

```yaml
---
title: "Page Title"
slug: "url-slug"
description: "SEO description"
category: "get-started"
position: 1
---
```
