---
title: "Project Overview"
last_updated: "2026-03-05"
contributors: ["arf"]
related_files: [
  "source/index.html.md",
  "config.rb",
  "Gemfile",
  "deploy.sh",
  "Dockerfile"
]
tags: ["architecture", "overview", "slate", "evocalize"]
---

# Project Overview

## What This Project Is

This is the **Evocalize Management API Reference** documentation site. It is built using [Slate](https://github.com/slatedocs/slate), a Ruby-based static site generator purpose-built for API documentation. The site provides a single-page, three-panel layout with navigation on the left, descriptions in the center, and code examples on the right.

The Evocalize platform is a SaaS marketing platform with capabilities for order management, audience targeting, content management, digital asset management, and user/group access control.

## Tech Stack

| Layer             | Technology                         |
|-------------------|------------------------------------|
| Static site gen   | Middleman 4.4 (Ruby)               |
| Markdown parser   | Redcarpet 3.5                      |
| Syntax highlight  | Rouge 3.21 (custom Monokai theme)  |
| CSS processing    | Sass + Autoprefixer                |
| Containerization  | Docker (multi-arch: amd64, arm64, ppc64le) |
| CI/CD             | GitHub Actions                     |
| Hosting           | GitHub Pages (gh-pages branch)     |

## Repository Structure

```
source/
  index.html.md          # Main page with YAML front matter listing includes
  includes/              # API documentation sections (Markdown)
    _request_and_response.md
    _jwt_token.md
    _user_and_group_management_api.md
    _access_control.md
    _content_management_api.md
    _content_access_v2.md
    _content_access_management.md
    _batch_report_api.md
    _orders.md
    _webhook_integration.md
    _audiences.md
    _digital_asset_management.md
    _errors.md
    _architecture_deep_linking.md
  stylesheets/           # Sass stylesheets
  javascripts/           # Client-side JS
  images/                # Static images
  fonts/                 # Web fonts

lib/
  multilang.rb           # Multi-language code block tab switching
  unique_head.rb         # Unique header ID generation
  toc_data.rb            # Table of contents tree builder
  monokai_sublime_slate.rb  # Custom syntax highlighting theme

config.rb                # Middleman configuration
Gemfile                  # Ruby dependencies
deploy.sh                # Deployment script
Dockerfile               # Docker build configuration

.github/workflows/
  build.yml              # CI build (Ruby 2.6-3.1 matrix)
  deploy.yml             # Main branch -> GitHub Pages root
  dev_deploy.yml         # Dev branch -> GitHub Pages /dev + Docker Hub
  publish.yml            # Release -> Docker Hub image
```

## API Surface Area

All management endpoints live under `https://partner-api-production.evocalize.com/management/v1/`.

### API Modules (in documentation order)

1. **Request & Response** - Standard format, pagination, async patterns
2. **JWT Token (SSO)** - HS256-based authentication for user SSO
3. **User & Group Management** - CRUD for users and groups
4. **Access Control** - Legacy V1 (deprecated)
5. **Content Management** - Repositories and content items
6. **Content Access V2** - Grantee-based permissions (current)
7. **Content Access Management** - Legacy content access (deprecated)
8. **Batch Report API** - Async operation status tracking
9. **Orders** - Purchase and subscription order lifecycle
10. **Webhook Integration** - Client leads webhook (Facebook, Google, TikTok) and Lead Concierge webhook events
11. **Audiences** - Audience placeholder management, CSV upload
12. **Digital Asset Management** - Media (image/video) CRUD and permissions
13. **Errors** - Standard error codes and HTTP status reference
14. **Deep Linking** - Client-side URL construction for Evocalize UI

### Authentication

Two approaches (headers on every request):

| Method          | Headers Required |
|-----------------|-----------------|
| Shared Secret   | `X-Evocalize-Client-Key-Id`, `X-Evocalize-Client-Key` |
| Signature-based | `X-Evocalize-Client-Key-Id`, `X-Evocalize-Timestamp`, `X-Evocalize-Signature` |

Signature uses HMAC SHA-256 over `clientKeyId + ":" + timestamp`.

### Common Patterns

- **Pagination**: 100 items/page, token-based (`pageToken`, `nextPageToken`, `previousPageToken`)
- **Batch operations**: Max 1000 items, async via `reportId`
- **Upsert semantics**: Create/update endpoints use same POST
- **Timestamps**: Unix seconds, Pacific Time
- **Standard response envelope**: `{ data, errors, nextPageToken, previousPageToken, metadata }`

## Build & Deployment

### Local Development

```bash
bundle install
bundle exec middleman serve    # http://localhost:4567
```

### Production Build

```bash
bundle exec middleman build --clean   # Output: /build
```

### Deployment Pipelines

| Trigger              | Target                        | Workflow         |
|----------------------|-------------------------------|------------------|
| Push to `main`       | GitHub Pages root             | deploy.yml       |
| Push to `dev`        | GitHub Pages `/dev` + Docker  | dev_deploy.yml   |
| GitHub Release       | Docker Hub                    | publish.yml      |
| PR / push (any)      | CI build check                | build.yml        |

## Adding New API Documentation

1. Create a new `_section_name.md` file in `source/includes/`
2. Add the section name (without `.md`) to the `includes` list in `source/index.html.md`
3. The order in the `includes` list determines the order on the page
4. Use Markdown with fenced code blocks for examples
5. Build locally to verify rendering before committing
