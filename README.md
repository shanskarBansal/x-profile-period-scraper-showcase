# X Profile Period Scraper - Showcase

This repository is a public showcase for a production Apify Actor that scrapes X (Twitter) profile posts inside user-defined date windows.

## What this project demonstrates

- Profile-level scraping across one or many usernames
- Strict date-range extraction in UTC
- Ownership-safe filtering to prevent cross-profile contamination
- Canonical redirect-safe post URLs (`https://x.com/i/status/<id>`)
- Rich structured post output for analytics pipelines
- Concurrency-aware crawling design for faster collection

## Problem it solves

When scraping social timelines, simple URL-pattern logic often leaks unrelated posts, especially when URLs can be rewritten or redirected. This implementation adds identity checks so only posts owned by the requested profile are stored.

## Key design decisions

1. GraphQL response parsing over DOM-only scraping
2. Strong input validation for URL + date fields
3. Profile ownership verification before persistence
4. Adaptive stopping logic to avoid premature range cutoffs
5. Output normalization for clean downstream usage

## Example output shape

```json
{
  "id": "2017989651190165690",
  "postUrl": "https://x.com/narendramodi/status/2017989651190165690",
  "redirectLink": "https://x.com/i/status/2017989651190165690",
  "createdAt": "2026-02-01T15:53:19.000Z",
  "text": "...",
  "thumbnail": "https://pbs.twimg.com/...jpg",
  "author": {
    "id": "18839785",
    "username": "narendramodi"
  },
  "metrics": {
    "likes": 20718,
    "reposts": 4105,
    "replies": 1660,
    "quotes": 141,
    "views": 5175985
  },
  "requestedProfileUsername": "narendramodi",
  "requestedProfileUserId": "18839785",
  "requestedProfileUrl": "https://x.com/narendramodi"
}
```

## Technology stack

- Node.js
- Apify SDK
- Crawlee + Playwright

## Status

- Production actor maintained in a separate repository
- This repository intentionally contains only documentation for portfolio/showcase purposes

## Contact

If you want the full implementation details, deployment pattern, or a custom variant (e.g., queue-based multi-account orchestration), open an issue.
